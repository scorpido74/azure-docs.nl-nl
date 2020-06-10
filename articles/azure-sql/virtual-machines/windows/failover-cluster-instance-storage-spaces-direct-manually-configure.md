---
title: SQL Server FCI op Azure Virtual Machines
description: In dit artikel wordt uitgelegd hoe u een SQL Server failover cluster instance (FCI) maakt op Azure Virtual Machines.
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
ms.openlocfilehash: 55ad535c965ae910b26900c2c555e21378ba49d9
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656738"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Een SQL Server-failovercluster configureren op virtuele machines van Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een SQL Server failovercluster (FCI) maakt in azure Virtual Machines in het Azure Resource Manager model. Deze oplossing maakt gebruik van [Windows Server 2016 data center edition opslagruimten direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) als een virtueel San op basis van een software die de opslag (gegevens schijven) synchroniseert tussen de knoop punten (virtuele machines van Azure) in een Windows-cluster. Opslagruimten Direct was nieuw in Windows Server 2016.

In het volgende diagram ziet u de volledige oplossing op Azure Virtual Machines:

![De volledige oplossing](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

In dit diagram ziet u:

- Twee virtuele machines in een Windows Server-failovercluster. Wanneer een virtuele machine zich in een failovercluster bevindt, wordt deze ook wel een *cluster knooppunt* of *knoop punt*genoemd.
- Elke virtuele machine heeft twee of meer gegevens schijven.
- Opslagruimten Direct synchroniseert de gegevens op de gegevens schijven en geeft de gesynchroniseerde opslag weer als een opslag groep.
- De opslag groep presenteert een Cluster Shared Volume (CSV) voor het failovercluster.
- De SQL Server cluster functie FCI gebruikt het CSV voor de gegevens stations.
- Een Azure-load balancer om het IP-adres voor de SQL Server FCI te bewaren.
- Een Azure Availability set bevat alle resources.

>[!NOTE]
>Alle Azure-resources in het diagram bevinden zich in dezelfde resource groep.

Zie [Windows Server 2016 data center edition opslagruimten direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)voor meer informatie over opslagruimten direct.

Opslagruimten Direct ondersteunt twee typen architecturen: geconvergeerd en Hyper-geconvergeerd. De architectuur in dit document is Hyper-geconvergeerd. Een Hyper-geconvergeerde infra structuur plaatst de opslag op de servers die als host fungeren voor de geclusterde toepassing. In deze architectuur bevindt de opslag zich op elk SQL Server FCI-knoop punt.

## <a name="licensing-and-pricing"></a>Licentie verlening en prijzen

In azure Virtual Machines kunt u een licentie SQL Server met behulp van PAYG (betalen per gebruik) of BYOL-VM-installatie kopieën (uw eigen licentie). Welk type installatie kopie u kiest, is van invloed op de manier waarop u in rekening wordt gebracht.

Met betalen per gebruik-licenties, een FCI (failover cluster instance) van SQL Server op Azure Virtual Machines kosten voor alle knoop punten van de FCI, met inbegrip van de passieve knoop punten. Zie [SQL Server Enterprise virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)voor meer informatie.

Als u Enterprise Agreement met Software Assurance hebt, kunt u één gratis passieve FCI-knoop punt voor elk actief knoop punt gebruiken. Als u gebruik wilt maken van dit voor deel in azure, gebruikt u BYOL VM-installatie kopieën en gebruikt u dezelfde licentie op de actieve en passieve knoop punten van de FCI. Zie [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)voor meer informatie.

Zie aan de [slag met SQL Server vm's](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)om betalen per gebruik en BYOL-licenties te vergelijken voor SQL Server op Azure virtual machines.

Zie [prijzen](https://www.microsoft.com/sql-server/sql-server-2017-pricing)voor volledige informatie over licentie SQL Server.

### <a name="example-azure-template"></a>Voor beeld van Azure-sjabloon

U kunt deze volledige oplossing in azure maken op basis van een sjabloon. Een voor beeld van een sjabloon is beschikbaar in de GitHub [Azure Quick](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)start-sjablonen. Dit voor beeld is niet ontworpen of getest voor een specifieke werk belasting. U kunt de sjabloon uitvoeren om een SQL Server FCI te maken met Opslagruimten Direct opslag die is verbonden met uw domein. U kunt de sjabloon evalueren en aanpassen voor uw doel einden.

## <a name="before-you-begin"></a>Voordat u begint

Er zijn enkele dingen die u moet weten voordat u begint.

### <a name="what-to-know"></a>Wat u moet weten

U moet een operationeel memorandum hebben van deze technologieën:

- [Windows-cluster technologieën](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Failover-cluster exemplaren SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Als u zich bewust bent van een Azure IaaS VM-gast-failovercluster, raden we u aan één NIC per server (cluster knooppunt) en één subnet te maken. Azure Networking heeft fysieke redundantie, waardoor extra Nic's en subnetten overbodig zijn op een Azure IaaS VM-gast cluster. In het cluster validatie rapport wordt u gewaarschuwd dat de knoop punten alleen bereikbaar zijn op één netwerk. U kunt deze waarschuwing over Azure IaaS VM-gast-failoverclusters negeren.

U moet ook algemene informatie over deze technologieën hebben:

- [Hyper-geconvergeerde oplossingen die gebruikmaken van Opslagruimten Direct in Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure-resourcegroepen](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Op dit moment worden SQL Server failover-cluster instanties in azure Virtual Machines alleen ondersteund in de [Lightweight-beheer modus](sql-vm-resource-provider-register.md#management-modes) van de [uitbrei ding SQL Server IaaS agent](sql-server-iaas-agent-extension-automate-management.md). Als u wilt overschakelen van de volledige extensie modus naar Lightweight, verwijdert u de resource van de **virtuele SQL-machine** voor de bijbehorende vm's en registreert u deze vervolgens bij de resource provider van de SQL-vm in de Lightweight-modus. Als u de bron van de **virtuele SQL-machine** verwijdert met behulp van de Azure Portal, **schakelt u het selectie vakje naast de juiste virtuele machine uit**. De volledige extensie ondersteunt functies zoals automatische back-ups, patches en Geavanceerd Portal beheer. Deze functies werken niet voor SQL-Vm's nadat de agent opnieuw is geïnstalleerd in de modus voor licht gewicht beheer.
> 

### <a name="what-to-have"></a>Wat u moet hebben

Voordat u de stappen in dit artikel hebt voltooid, hebt u het volgende nodig:

- Een Microsoft Azure-abonnement
- Een Windows-domein in azure Virtual Machines
- Een account met machtigingen voor het maken van objecten op zowel virtuele machines als in Active Directory
- Een virtueel Azure-netwerk en subnet met voldoende IP-adres ruimte voor deze onderdelen:
   - Zowel virtuele machines
   - Het IP-adres van het failovercluster
   - Een IP-adres voor elke FCI
- DNS geconfigureerd op het Azure-netwerk, wijst naar de domein controllers

Als aan deze vereisten is voldaan, kunt u beginnen met het bouwen van uw failovercluster. De eerste stap is het maken van de virtuele machines.

## <a name="step-1-create-the-virtual-machines"></a>Stap 1: de virtuele machines maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw abonnement.

1. [Maak een Azure-beschikbaarheidsset](../../../virtual-machines/linux/tutorial-availability-sets.md).

   Met de beschikbaarheidsset worden virtuele machines gegroepeerd op fout domeinen en update domeinen. Het zorgt ervoor dat uw toepassing niet wordt beïnvloed door afzonderlijke storings punten, zoals de netwerk switch of de energie-eenheid van een rek met servers.

   Als u de resource groep voor uw virtuele machines nog niet hebt gemaakt, doet u dit wanneer u een Azure-beschikbaarheidsset maakt. Voer de volgende stappen uit als u de Azure Portal voor het maken van de beschikbaarheidsset wilt gebruiken:

   1. Selecteer in de Azure Portal **een resource maken** om de Azure Marketplace te openen. Zoeken naar **beschikbaarheidsset**.
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

   Voorziet in twee SQL Server virtuele machines in de beschikbaarheidsset van Azure. Zie [een SQL Server virtuele machine inrichten in de Azure Portal](create-sql-vm-portal.md)voor instructies.

   Plaats beide virtuele machines:

   - In dezelfde Azure-resource groep als uw beschikbaarheidsset
   - Op hetzelfde netwerk als uw domein controller
   - Op een subnet met voldoende IP-adres ruimte voor zowel virtuele machines als alle Failoverclusterinstanties die u uiteindelijk kunt gebruiken op het cluster
   - In de beschikbaarheidsset van Azure

      >[!IMPORTANT]
      >U kunt de beschikbaarheidsset niet instellen of wijzigen nadat u een virtuele machine hebt gemaakt.
      >

   Kies een installatie kopie van Azure Marketplace. U kunt een Azure Marketplace-installatie kopie gebruiken die Windows Server en SQL Server bevat, of u moet er een gebruiken die alleen Windows Server bevat. Zie [overzicht van SQL Server op Azure virtual machines](sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie.

   De officiële SQL Server installatie kopieën in de Azure-galerie bevatten een geïnstalleerd SQL Server exemplaar, de SQL Server installatie software en de vereiste sleutel.

   Kies de juiste afbeelding, op basis van hoe u wilt betalen voor de SQL Server licentie:

   - **Licenties voor betalen per gebruik**. De kosten per seconde van deze installatie kopieën omvatten de SQL Server licenties:
      - **SQL Server 2016 Enter prise op Windows Server 2016 Data Center**
      - **SQL Server 2016 Standard op Windows Server 2016 Data Center**
      - **SQL Server 2016-ontwikkelaar op Windows Server 2016 Data Center**

   - **Neem uw eigen licentie mee (BYOL)**

      - **BYOL SQL Server 2016 Enter prise op Windows Server 2016 Data Center**
      - **BYOL SQL Server 2016 Standard op Windows Server 2016 Data Center**

   >[!IMPORTANT]
   >Nadat u de virtuele machine hebt gemaakt, verwijdert u het vooraf geïnstalleerde zelfstandige SQL Server exemplaar. U gebruikt het vooraf geïnstalleerde SQL Server medium om de SQL Server FCI te maken nadat u het failovercluster en de Opslagruimten Direct hebt ingesteld.
   >

   U kunt ook installatie kopieën van Azure Marketplace gebruiken die alleen het besturings systeem bevatten. Kies een installatie kopie van **Windows Server 2016 Data Center** en installeer de SQL Server FCI nadat u het failovercluster en de opslagruimten direct hebt ingesteld. Deze installatie kopie bevat geen SQL Server-installatie media. Plaats de SQL Server-installatie media op een locatie waar u deze voor elke server kunt uitvoeren.

1. Nadat Azure uw virtuele machines heeft gemaakt, kunt u met behulp van RDP (Remote Desktop Protocol) verbinding maken met elk van deze computers.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met behulp van RDP, wordt u gevraagd of u wilt toestaan dat de PC kan worden gedetecteerd in het netwerk. Selecteer **Ja**.

1. Als u een van de op SQL Server gebaseerde installatie kopieën voor virtuele machines gebruikt, verwijdert u het SQL Server exemplaar.

   1. Klik in **Program ma's en onderdelen**met de rechter muisknop op **Microsoft SQL Server 2016 (64-bits)** en selecteer **verwijderen/wijzigen**.
   1. Selecteer **Verwijderen**.
   1. Selecteer het standaard exemplaar.
   1. Verwijder alle functies onder **Data Base Engine-Services**. Verwijder **gedeelde onderdelen**niet. U ziet iets als de volgende scherm afbeelding:

      ![Functies selecteren](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. Selecteer **volgende**en selecteer vervolgens **verwijderen**.

1. <a name="ports"></a>Open de firewall poorten.

   Open op elke virtuele machine deze poorten op het Windows Firewall:

   | Functie | TCP-poort | Opmerkingen
   | ------ | ------ | ------
   | SQL Server | 1433 | Normale poort voor standaard exemplaren van SQL Server. Als u een installatie kopie uit de galerie hebt gebruikt, wordt deze poort automatisch geopend.
   | Statustest | 59999 | Een open TCP-poort. Configureer in een latere stap de load balancer [Health probe](#probe) en het cluster om deze poort te gebruiken.  

1. Voeg opslag toe aan de virtuele machine. Zie [opslag toevoegen](../../../virtual-machines/linux/disks-types.md)voor gedetailleerde informatie.

   Beide virtuele machines hebben ten minste twee gegevens schijven nodig.

   Koppel onbewerkte schijven en schijven die geen NTFS-geformatteerd zijn.

      >[!NOTE]
      >Als u schijven met NTFS-indeling koppelt, kunt u alleen Opslagruimten Direct inschakelen zonder controle op schijf geschiktheid. 
      > 

   Koppel mini maal twee Premium-Ssd's aan elke virtuele machine. We raden ten minste P30-schijven (1 TB) aan.

   Stel host-caching in op **alleen-lezen**.

   De opslag capaciteit die u gebruikt in productie omgevingen is afhankelijk van uw werk belasting. De waarden die in dit artikel worden beschreven, zijn voor demonstratie-en test doeleinden.

1. [Voeg de virtuele machines toe aan uw vooraf bestaande domein](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Nadat u de virtuele machines hebt gemaakt en geconfigureerd, kunt u het failovercluster instellen.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Stap 2: Configureer het Windows Server-failovercluster met Opslagruimten Direct

U configureert nu het failovercluster met Opslagruimten Direct. In deze sectie voert u de volgende stappen uit:

1. Voeg de functie Windows Server Failover Clustering toe.
1. Valideer het cluster.
1. Maak het failovercluster.
1. Maak de cloudwitness.
1. Voeg opslag toe.

### <a name="add-windows-server-failover-clustering"></a>Windows Server failover clustering toevoegen

1. Maak verbinding met de eerste virtuele machine met RDP met behulp van een domein account dat lid is van de lokale beheerders en dat gemachtigd is om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Voeg Failover Clustering toe aan elke virtuele machine](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

Zie voor meer informatie over de volgende stappen de instructies onder stap 3 van de [Hyper-geconvergeerde oplossing met behulp van opslagruimten direct in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Het cluster valideren

Valideer het cluster in de gebruikers interface of met behulp van Power shell.

Als u het cluster wilt valideren met behulp van de gebruikers interface, voert u de volgende stappen uit op een van de virtuele machines:

1. Klik onder **Serverbeheer**op **extra**en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer onder **Failoverclusterbeheer** **actie**en selecteer vervolgens **configuratie valideren**.
1. Selecteer **Next**.
1. Voer onder **servers of een cluster selecteren**de namen van beide virtuele machines in.
1. Onder **test opties**selecteert u **alleen geselecteerde tests uitvoeren**. Selecteer **Next**.
1. Selecteer onder **selectie testen**alle tests, met uitzonde ring van **opslag**, zoals hier wordt weer gegeven:

   ![Cluster validatie tests selecteren](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecteer **Next**.
1. Klik onder **bevestiging**op **volgende**.

Met de wizard een configuratie valideren worden de validatie tests uitgevoerd.

Als u het cluster wilt valideren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.

### <a name="create-the-failover-cluster"></a>Het failovercluster maken

Als u het failovercluster wilt maken, hebt u het volgende nodig:

- De namen van de virtuele machines die de cluster knooppunten worden
- Een naam voor het failovercluster
- Een IP-adres voor het failovercluster <br/>
  U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de cluster knooppunten.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 tot en met Windows Server 2016

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2008 via Windows Server 2016. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2019. Zie voor meer informatie [failover cluster: Cluster Network object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Een cloudwitness maken

Cloudwitness is een nieuw type cluster quorum-Witness dat is opgeslagen in een Azure Storage-blob. Dit verwijdert de nood zaak van een afzonderlijke virtuele machine die als host fungeert voor een Witness-share.

1. [Maak een cloudwitness voor het failovercluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Maak een BLOB-container.

1. Sla de toegangs sleutels en de container-URL op.

1. Configureer de quorum-Witness van het failovercluster. Zie [Configure the quorumwitness in de gebruikers interface](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Opslag toevoegen

De schijven voor Opslagruimten Direct moeten leeg zijn. Ze kunnen geen partities of andere gegevens bevatten. Volg [de stappen in deze hand leiding](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)om de schijven op te schonen.

1. [Store Spaces direct inschakelen](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Met het volgende Power shell-script kunt Opslagruimten Direct:  

   ```powershell
   Enable-ClusterS2D
   ```

   In **Failoverclusterbeheer**kunt u nu de opslag groep zien.

1. [Maak een volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Opslagruimten Direct maakt automatisch een opslag groep wanneer u deze inschakelt. U bent nu klaar om een volume te maken. De Power shell-cmdlet `New-Volume` automatiseert het proces voor het maken van een volume. Dit proces omvat het format teren, het toevoegen van het volume aan het cluster en het maken van een Cluster Shared Volume (CSV). In dit voor beeld wordt een CSV-bestand van 800 GB gemaakt:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Nadat deze opdracht is voltooid, wordt een volume van 800 GB gekoppeld als een cluster bron. Het volume bevindt zich op `C:\ClusterStorage\Volume1\` .

   Deze scherm afbeelding toont een Cluster Shared Volume met Opslagruimten Direct:

   ![Cluster Shared Volume](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Stap 3: failover van failover-cluster testen

Controleer in **Failoverclusterbeheer**of u de opslag resource naar het andere cluster knooppunt kunt verplaatsen. Als u verbinding kunt maken met het failovercluster met behulp van **Failoverclusterbeheer** en de opslag van het ene naar het andere knoop punt verplaatst, bent u klaar om de FCI te configureren.

## <a name="step-4-create-the-sql-server-fci"></a>Stap 4: de SQL Server FCI maken

Nadat u het failovercluster en alle cluster onderdelen, inclusief opslag, hebt geconfigureerd, kunt u de SQL Server-FCI maken.

1. Maak verbinding met de eerste virtuele machine met behulp van RDP.

1. Zorg er in **Failoverclusterbeheer**voor dat alle kern cluster bronnen zich op de eerste virtuele machine bevinden. Verplaats, indien nodig, alle resources naar die virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full` . Selecteer **Setup**.

1. In **SQL Server Installation Center**selecteert u **installatie**.

1. Selecteer **nieuwe SQL Server failover-cluster installatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevens directory's moeten zich op geclusterde opslag beslaan. Met Opslagruimten Direct is het geen gedeelde schijf, maar een koppel punt naar een volume op elke server. Opslagruimten Direct synchroniseert het volume tussen beide knoop punten. Het volume wordt als Cluster Shared Volume weer gegeven aan het cluster. Gebruik het CSV-koppel punt voor de gegevens directory's.

   ![Gegevens directory's](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Nadat u de instructies in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met behulp van RDP.

1. Open het **SQL Server-installatie centrum**. Selecteer **installatie**.

1. Selecteer **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en de server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt die SQL Server bevat, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u een van deze installatie kopieën niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="step-5-create-the-azure-load-balancer"></a>Stap 5: de Azure load balancer maken

In azure Virtual Machines gebruiken clusters een load balancer om een IP-adres op te slaan dat op één cluster knooppunt tegelijk moet zijn. In deze oplossing bevat de load balancer het IP-adres voor de SQL Server FCI.

Zie [een Azure-Load Balancer maken en configureren](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)voor meer informatie.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Een taakverdeler maken in Azure-portal

De load balancer maken:

1. Ga in het Azure Portal naar de resource groep die de virtuele machines bevat.

1. Selecteer **Toevoegen**. Zoek in de Azure Marketplace naar **Load Balancer**. Selecteer **Load Balancer**.

1. Selecteer **Maken**.

1. Configureer de load balancer met:

   - **Abonnement**: uw Azure-abonnement.
   - **Resource groep**: de resource groep die uw virtuele machines bevat.
   - **Naam**: een naam die de Load Balancer aanduidt.
   - **Regio**: de Azure-locatie waar uw virtuele machines zich bevinden.
   - **Type**: ofwel openbaar of privé. Er kan vanuit het virtuele netwerk toegang worden verkregen tot een persoonlijke load balancer. De meeste Azure-toepassingen kunnen een privé-load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang moet hebben tot SQL Server, gebruik dan een open bare load balancer.
   - **SKU**: Standard.
   - **Virtueel netwerk**: hetzelfde netwerk als de virtuele machines.
   - **IP-adres toewijzing**: statisch. 
   - **Persoonlijk IP-adres**: het IP-adres dat u hebt toegewezen aan de resource van het SQL Server FCI-cluster netwerk.

 De volgende scherm afbeelding toont de gebruikers interface **Create Load Balancer** :

   ![De load balancer instellen](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>De load balancer back-end-groep configureren

1. Ga terug naar de Azure-resource groep die de virtuele machines bevat en zoek de nieuwe load balancer. Mogelijk moet u de weer gave van de resource groep vernieuwen. Selecteer de load balancer.

1. Selecteer **back-endservers**en selecteer vervolgens **toevoegen**.

1. Koppel de back-end-pool met de beschikbaarheidsset die de virtuele machines bevat.

1. Onder **IP-configuraties**voor het doelnet **werk selecteert u virtuele machine** en kiest u de virtuele machines die als cluster knooppunten zullen worden beschouwd. Zorg ervoor dat u alle virtuele machines opneemt die als host moeten fungeren voor de FCI.

1. Selecteer **OK** om de back-end-pool te maken.

### <a name="configure-a-load-balancer-health-probe"></a>Een load balancer-statustest configureren

1. Selecteer op de Blade load balancer **status controles**.

1. Selecteer **Toevoegen**.

1. Stel op de Blade **status test toevoegen** <a name="probe"></a> de para meters Health probe in.

   - **Naam**: een naam voor de status test
   - **Protocol**: TCP 
   - **Poort**: Stel in [deze stap](#ports) de poort in die u in de firewall hebt gemaakt voor de status test <br/>In dit artikel gebruikt het voor beeld TCP-poort `59999` .
   - **Interval**: 5 seconden.
   - **Drempel waarde voor onjuiste status**: 2 opeenvolgende fouten

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

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Stap 6: het cluster voor de test configureren

Stel de para meter cluster probe Port in Power shell in.

Als u de para meter voor de cluster test poort wilt instellen, werkt u de variabelen in het volgende script bij met waarden uit uw omgeving. Verwijder de punt haken ( `<` en `>` ) van het script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

In de volgende lijst worden de waarden beschreven die u moet bijwerken:

   - `<Cluster Network Name>`: De naam van het Windows Server-failovercluster voor het netwerk. Klik in **Failoverclusterbeheer**  >  **netwerken**met de rechter muisknop op het netwerk en selecteer **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** .

   - `<SQL Server FCI IP Address Resource Name>`: De naam van de FCI-IP-adres bron van het SQL Server. Klik in **Failoverclusterbeheer**  >  **rollen**onder de rol SQL Server FCI onder **Server naam**met de rechter muisknop op de IP-adres bron en selecteer **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** . 

   - `<ILBIP>`: Het IP-adres van ILB. Dit adres wordt geconfigureerd in de Azure Portal als het front-end-adres van de ILB. Dit is ook het SQL Server IP-adres van FCI. U kunt deze in **Failoverclusterbeheer** vinden op dezelfde eigenschappen pagina waar u de hebt opgeslagen `<SQL Server FCI IP Address Resource Name>` .  

   - `<nnnnn>`: De test poort die u hebt geconfigureerd in de load balancer Health probe. Alle ongebruikte TCP-poort is geldig.

>[!IMPORTANT]
>Het subnetmasker voor de cluster parameter moet het TCP IP-broadcast adres zijn: `255.255.255.255` .
>

Nadat u de cluster test hebt ingesteld, kunt u alle cluster parameters in Power shell zien. Voer dit script uit:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Stap 7: FCI failover testen

Test de failover van de FCI om de functionaliteit van het cluster te valideren. Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server FCI-cluster knooppunten met behulp van RDP.

1. Open **Failoverclusterbeheer**. Selecteer **rollen**. U ziet welk knoop punt eigenaar is van de SQL Server rol FCI.

1. Klik met de rechter muisknop op de SQL Server FCI rol.

1. Selecteer **verplaatsen**en selecteer vervolgens **best mogelijke knoop punt**.

**Failoverclusterbeheer** toont de functie en de bijbehorende resources worden offline gezet. De resources worden vervolgens verplaatst en online gezet op het andere knoop punt.

### <a name="test-connectivity"></a>Connectiviteit testen

Als u de verbinding wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de naam van de SQL Server FCI.

>[!NOTE]
>Als dat het geval is, kunt u [SQL Server Management Studio downloaden](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="limitations"></a>Beperkingen

Azure Virtual Machines ondersteunt micro soft Distributed Transaction Coordinator (MSDTC) op Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en een [standaard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

Op Azure Virtual Machines wordt MSDTC niet ondersteund in Windows Server 2016 of eerder omdat:

- De geclusterde MSDTC-bron kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u in Windows Server 2016 een MSDTC-bron maakt, wordt er geen gedeelde opslag weer gegeven die beschikbaar is voor gebruik, zelfs als de opslag ruimte beschikbaar is. Dit probleem is opgelost in Windows Server 2019.
- Met de basis load balancer worden geen RPC-poorten afgehandeld.

## <a name="see-also"></a>Zie ook

[Opslagruimten Direct instellen met extern bureau blad (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Een Hyper-geconvergeerde oplossing met Opslagruimten Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Overzicht van Opslagruimten Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server ondersteuning voor Opslagruimten Direct](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
