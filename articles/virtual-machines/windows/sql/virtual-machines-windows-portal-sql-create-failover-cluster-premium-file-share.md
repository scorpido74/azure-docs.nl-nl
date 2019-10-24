---
title: SQL Server FCI met Premium-bestands share-Azure Virtual Machines
description: In dit artikel wordt uitgelegd hoe u een SQL Server failover-cluster exemplaar maakt met behulp van een Premium-bestands share op Azure Virtual Machines.
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
ms.openlocfilehash: cebfb5315a7a0b7cf92dbb9e3e77ff4c35851aa8
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72758167"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>SQL Server failover-cluster exemplaar configureren met Premium-bestands share op Azure Virtual Machines

In dit artikel wordt uitgelegd hoe u een SQL Server failovercluster (FCI) maakt op virtuele Azure-machines met behulp van een [Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Premium-bestands shares zijn consistente bestands shares met een SSD-lage latentie die volledig worden ondersteund voor gebruik met een failover-cluster exemplaar voor SQL Server 2012 en nieuwere versie van Windows Server 2012 en nieuwer. Premium-bestands shares bieden meer flexibiliteit, waardoor u de bestands share zonder uitval tijd kunt verg Roten of verkleinen en schalen. 


## <a name="before-you-begin"></a>Voordat u begint

Er zijn enkele dingen die u moet weten en een aantal dingen die u nodig hebt voordat u verdergaat.

U moet een operationeel memorandum hebben van de volgende technologieën:

- [Windows-cluster technologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server failover-cluster exemplaren](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Een belang rijk verschil is dat in een Azure IaaS VM-failovercluster wordt aangeraden één NIC per server (cluster knooppunt) en één subnet. Azure Networking heeft fysieke redundantie waardoor extra Nic's en subnetten onnodig worden uitgevoerd op een Azure IaaS VM-gast cluster. Hoewel er in het cluster validatie rapport een waarschuwing wordt gegeven dat de knoop punten alleen bereikbaar zijn op één netwerk, kan deze waarschuwing veilig worden genegeerd op virtuele Azure IaaS VM-failoverclusters. 

Daarnaast moet u een algemene uitleg hebben van de volgende technologieën:

- [Azure Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-resource groepen](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Op dit moment worden SQL Server failover-cluster exemplaren op virtuele machines van Azure alleen ondersteund in de [Lightweight](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) -beheer modus van de [SQL Server IaaS agent-extensie](virtual-machines-windows-sql-server-agent-extension.md). Als u wilt overschakelen van de volledige extensie modus naar Lightweight, verwijdert u de resource van de **virtuele SQL-machine** voor de bijbehorende vm's en registreert u deze vervolgens bij de resource provider van de SQL-vm in de [Lightweight](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) -modus. Als u de bron van de **virtuele SQL-machine** verwijdert met behulp van de Azure Portal, schakelt u het selectie vakje naast de juiste virtuele machine uit. De volledige extensie ondersteunt functies zoals automatische back-ups, patches en Geavanceerd Portal beheer. Deze functies werken niet voor SQL-Vm's nadat de agent opnieuw is geïnstalleerd in de modus voor [licht gewicht](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) beheer.

### <a name="workload-consideration"></a>Werklast vergoeding

Premium-bestands shares bieden IOPS en hebben een capaciteit die voldoet aan de behoeften van veel werk belastingen. Voor intensieve i/o-workloads kunt u echter [SQL Server FCI met opslagruimten direct](virtual-machines-windows-portal-sql-create-failover-cluster.md) op basis van beheerde Premium-schijven of Ultra-disks.  

Controleer de IOPS-activiteit van uw huidige omgeving en controleer of Premium files de IOPS bevat die u nodig hebt voordat u een implementatie of migratie start. Gebruik schijf items van de Windows-prestatie meter en bewaak de totale IOPS (schijf overdrachten/sec) en de door Voer (schijf bytes per seconde) die zijn vereist voor SQL Server gegevens, logboeken en tijdelijke DB-bestanden. Veel werk belastingen hebben een bursting van IO zodat het een goed idee is om tijdens zware gebruiks perioden te controleren en de maximale IOPS en de gemiddelde IOPS te noteren. Premium-bestands shares bieden IOPS op basis van de grootte van de share. Premium-bestanden bieden ook extra bursting, waar u uw IO kunt inzoomen om de basislijn hoeveelheid tot een uur te laten opkomen. 

Zie [prestatie lagen voor bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers)voor meer informatie over de prestaties van Premium-bestands shares. 

### <a name="licensing-and-pricing"></a>Licentie verlening en prijzen

In azure Virtual Machines kunt u een licentie SQL Server met betalen per gebruik (PAYG) of uw eigen licentie-VM-installatie kopieën (BYOL) meenemen. Welk type installatie kopie u kiest, is van invloed op de manier waarop u in rekening wordt gebracht.

Met PAYG-licenties, een FCI (failover cluster instance) van SQL Server op Azure Virtual Machines kosten voor alle knoop punten van FCI, met inbegrip van de passieve knoop punten. Zie [SQL Server Enterprise virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)voor meer informatie. 

Klanten met Enterprise Agreement met Software Assurance hebben het recht om één gratis passief FCI-knoop punt voor elk actief knoop punt te gebruiken. Als u gebruik wilt maken van dit voor deel in azure, gebruikt u BYOL VM-installatie kopieën en gebruikt u dezelfde licentie op de actieve en passieve knoop punten van de FCI. Zie [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)voor meer informatie.

Zie aan de [slag met SQL-vm's](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)om payg-en BYOL-licenties te vergelijken voor SQL Server op Azure virtual machines.

Zie [prijzen](https://www.microsoft.com/sql-server/sql-server-2017-pricing)voor volledige informatie over licentie SQL Server.

### <a name="limitations"></a>Beperkingen

- FileStream wordt niet ondersteund voor een failovercluster met een Premium-bestands share. Implementeer uw cluster met behulp van [opslagruimten direct](virtual-machines-windows-portal-sql-create-failover-cluster.md)om FileStream te gebruiken. 

## <a name="prerequisites"></a>Vereisten 

Voordat u de instructies in dit artikel volgt, hebt u het volgende nodig:

- Een Microsoft Azure-abonnement.
- Een Windows-domein op virtuele machines van Azure.
- Een account met machtigingen voor het maken van objecten in de virtuele Azure-machine.
- Een virtueel Azure-netwerk en subnet met voldoende IP-adres ruimte voor de volgende onderdelen:
   - Zowel virtuele machines.
   - Het IP-adres van het failovercluster.
   - Een IP-adres voor elke FCI.
- DNS geconfigureerd op het Azure-netwerk, die verwijst naar de domein controllers.
- Een [Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md) op basis van de opslag limiet van uw Data Base voor uw gegevens bestanden. 
- Een bestands share voor back-ups die afwijkt van de Premium-bestands share die wordt gebruikt voor uw gegevens bestanden. Deze bestands share kan Standard of Premium zijn. 

Als aan deze vereisten is voldaan, kunt u door gaan met het bouwen van uw failovercluster. De eerste stap is het maken van de virtuele machines.

## <a name="step-1-create-virtual-machines"></a>Stap 1: virtuele machines maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw abonnement.

1. [Maak een Azure-beschikbaarheidsset](../tutorial-availability-sets.md).

   Met de beschikbaarheidsset worden virtuele machines gegroepeerd op fout domeinen en update domeinen. De beschikbaarheidsset zorgt ervoor dat uw toepassing niet wordt beïnvloed door afzonderlijke storings punten, zoals de netwerk switch of de energie-eenheid van een rek van servers.

   Als u de resource groep voor uw virtuele machines niet hebt gemaakt, doet u dit wanneer u een Azure-beschikbaarheidsset maakt. Als u de Azure Portal gebruikt voor het maken van de beschikbaarheidsset, voert u de volgende stappen uit:

   - Klik in de Azure Portal op **+** om de Azure Marketplace te openen. Zoeken naar **beschikbaarheidsset**.
   - Klik op **beschikbaarheidsset**.
   - Klik op **Maken**.
   - Stel op de Blade **beschikbaarheidsset maken** de volgende waarden in:
      - **Naam**: een naam voor de beschikbaarheidsset.
      - **Abonnement**: uw Azure-abonnement.
      - **Resource groep**: als u een bestaande groep wilt gebruiken, klikt u op **bestaande gebruiken** en selecteert u de groep in de vervolg keuzelijst. Kies anders **nieuwe maken** en typ een naam voor de groep.
      - **Locatie**: Stel de locatie in waar u uw virtuele machines wilt maken.
      - **Fout domeinen**: gebruik de standaard waarde (3).
      - **Update domeinen**: gebruik de standaard waarde (5).
   - Klik op **maken** om de beschikbaarheidsset te maken.

1. Maak de virtuele machines in de beschikbaarheidsset.

   Voorziet in twee SQL Server virtuele machines in de beschikbaarheidsset van Azure. Zie [een SQL Server virtuele machine inrichten in de Azure Portal](virtual-machines-windows-portal-sql-server-provision.md)voor instructies.

   Plaats beide virtuele machines:

   - In dezelfde Azure-resource groep waarin uw beschikbaarheidsset zich bevindt.
   - Op hetzelfde netwerk als uw domein controller.
   - Op een subnet met voldoende IP-adres ruimte voor zowel virtuele machines als alle Failoverclusterinstanties die u uiteindelijk kunt gebruiken op dit cluster.
   - In de beschikbaarheidsset van Azure.   

      >[!IMPORTANT]
      >U kunt de beschikbaarheidsset niet instellen of wijzigen nadat een virtuele machine is gemaakt.

   Kies een installatie kopie in de Azure Marketplace. U kunt een Marketplace-installatie kopie gebruiken die Windows Server en SQL Server, of alleen de Windows-Server bevat. Zie [overzicht van SQL Server op Azure](virtual-machines-windows-sql-server-iaas-overview.md) voor meer informatie virtual machines

   De officiële SQL Server installatie kopieën in de Azure-galerie bevatten een geïnstalleerd SQL Server exemplaar, plus de SQL Server installatie software en de vereiste sleutel.

   >[!IMPORTANT]
   > Nadat u de virtuele machine hebt gemaakt, verwijdert u het vooraf geïnstalleerde zelfstandige SQL Server exemplaar. U gebruikt het vooraf geïnstalleerde SQL Server medium om de SQL Server FCI te maken nadat u het failovercluster en Premium file share als opslag hebt geconfigureerd. 

   U kunt ook Azure Marketplace-installatie kopieën gebruiken met alleen het besturings systeem. Kies een installatie kopie van **Windows Server 2016 Data Center** en installeer de SQL Server FCI na het configureren van het failovercluster en Premium file share als opslag. Deze installatie kopie bevat geen SQL Server-installatie media. Plaats de installatie media op een locatie waar u de SQL Server-installatie voor elke server kunt uitvoeren. 

1. Nadat Azure uw virtuele machines heeft gemaakt, maakt u verbinding met elke virtuele machine met RDP.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met RDP, wordt u gevraagd of u wilt toestaan dat deze PC kan worden gedetecteerd in het netwerk. Klik op **Ja**.

1. Als u een van de op SQL Server gebaseerde installatie kopieën voor virtuele machines gebruikt, verwijdert u het SQL Server exemplaar.

   - Klik in **Program ma's en onderdelen**met de rechter muisknop op **Microsoft SQL Server 201_ (64-bits)** en klik vervolgens op **verwijderen/wijzigen**.
   - Klik op **Verwijderen**.
   - Selecteer het standaard exemplaar.
   - Verwijder alle functies onder **Data Base Engine-Services**. Geen **gedeelde onderdelen**verwijderen. Zie de volgende afbeelding:

      ![Onderdelen verwijderen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klik op **volgende**en klik vervolgens op **verwijderen**.

1. <a name="ports"></a>Open de firewall poorten.

   Open op elke virtuele machine de volgende poorten op het Windows Firewall.

   | Doel | TCP-poort | Opmerkingen
   | ------ | ------ | ------
   | SQL Server | 1433 | Normale poort voor standaard exemplaren van SQL Server. Als u een installatie kopie uit de galerie hebt gebruikt, wordt deze poort automatisch geopend.
   | Statustest | 59999 | Een open TCP-poort. Configureer in een latere stap de load balancer [Health probe](#probe) en het cluster om deze poort te gebruiken.   
   | Bestandsshare | 445 | Poort die wordt gebruikt door de bestands share service. 

1. [Voeg de virtuele machines toe aan uw vooraf bestaande domein](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Nadat de virtuele machines zijn gemaakt en geconfigureerd, kunt u de Premium-bestands share configureren.

## <a name="step-2-mount-premium-file-share"></a>Stap 2: Premium-bestands share koppelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en ga naar uw opslag account.
1. Ga naar **Bestands shares** onder **Bestands service** en selecteer de Premium-bestands share die u wilt gebruiken voor uw SQL-opslag. 
1. Selecteer **verbinding** om de Connection String voor uw bestands share weer te geven. 
1. Selecteer de stationsletter die u wilt gebruiken in de vervolg keuzelijst en Kopieer beide code blokken naar een Klad blok.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Beide Power shell-opdrachten kopiëren van de bestands share Connect Portal":::

1. RDP in de SQL Server-VM met behulp van het account dat door uw SQL Server FCI wordt gebruikt voor het service account. 
1. Een beheer-Power shell-opdracht console starten. 
1. Voer de opdrachten uit vanuit de portal die u eerder hebt opgeslagen. 
1. Ga naar de share met bestanden Verkenner of het dialoog venster **uitvoeren** (Windows-toets + r) met behulp van het netwerkpad `\\storageaccountname.file.core.windows.net\filesharename`. Voorbeeld: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Maak ten minste één map op de zojuist verbonden bestands share om uw SQL-gegevens bestanden in te brengen. 
1. Herhaal deze stappen op elke SQL Server virtuele machine die u aan het cluster wilt deel nemen. 

  > [!IMPORTANT]
  > Overweeg het gebruik van een afzonderlijke bestands share voor back-upbestanden om de IOPS-en grootte capaciteit van deze share voor gegevens en logboek bestanden op te slaan. U kunt een Premium-of standaard bestands share gebruiken voor back-upbestanden

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Stap 3: het failovercluster met een bestands share configureren 

De volgende stap is het configureren van het failovercluster. In deze stap gaat u de volgende substaps uitvoeren:

1. Functie Windows Failover Clustering toevoegen
1. Het cluster valideren
1. Het failovercluster maken
1. De cloudwitness maken


### <a name="add-windows-failover-clustering-feature"></a>Functie Windows Failover Clustering toevoegen

1. Als u wilt beginnen, maakt u verbinding met de eerste virtuele machine met RDP met behulp van een domein account dat lid is van lokale beheerders en beschikt over machtigingen voor het maken van objecten in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Voeg de functie Failover Clustering toe aan elke virtuele machine](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Als u de functie Failover Clustering wilt installeren vanuit de gebruikers interface, voert u de volgende stappen uit op beide virtuele machines.
   - Klik in **Serverbeheer**op **beheren**en klik vervolgens op **functies en onderdelen toevoegen**.
   - Klik in de **wizard functies en onderdelen toevoegen**op **volgende** totdat u **onderdelen selecteert**.
   - Klik in **functies selecteren**op **failover clustering**. Neem alle vereiste functies en de beheer hulpprogramma's op. Klik op **onderdelen toevoegen**.
   - Klik op **volgende** en klik vervolgens op **volt ooien** om de onderdelen te installeren.

   Als u de functie Failover Clustering wilt installeren met Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Het cluster valideren

Deze hand leiding verwijst naar instructies onder [cluster valideren](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valideer het cluster in de gebruikers interface of met Power shell.

Voer de volgende stappen uit op een van de virtuele machines om het cluster te valideren met de gebruikers interface.

1. Klik in **Serverbeheer**op **extra**en klik vervolgens op **Failoverclusterbeheer**.
1. Klik in **Failoverclusterbeheer**op **actie**en klik vervolgens op **configuratie valideren...** .
1. Klik op **Volgende**.
1. Typ de naam van beide virtuele machines op **servers of een cluster selecteren**.
1. Kies op **test opties** **alleen tests uitvoeren die ik selecteer**. Klik op **Volgende**.
1. Neem bij **selectie testen**alle tests behalve **opslag** en **opslagruimten direct**op. Zie de volgende afbeelding:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Cluster validatie tests":::

1. Klik op **Volgende**.
1. Klik op de **bevestiging**op **volgende**.

Met de **wizard een configuratie valideren** worden de validatie tests uitgevoerd.

Als u het cluster met Power shell wilt valideren, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.

### <a name="create-the-failover-cluster"></a>Het failovercluster maken


Als u het failovercluster wilt maken, hebt u het volgende nodig:
- De namen van de virtuele machines die de cluster knooppunten worden.
- Een naam voor het failovercluster
- Een IP-adres voor het failovercluster. U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de cluster knooppunten.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

Met de volgende Power Shell maakt u een failovercluster voor **Windows Server 2012-2016**. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres uit het Azure-VNET:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Met de volgende Power Shell maakt u een failovercluster voor Windows Server 2019.  Raadpleeg voor meer informatie het blog- [failovercluster: cluster-netwerk object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres uit het Azure-VNET:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Een cloudwitness maken

Cloudwitness is een nieuw type cluster quorum-Witness dat is opgeslagen in een Azure Storage Blob. Hierdoor is er geen afzonderlijke virtuele machine nodig die als host fungeert voor een Witness-share.

1. [Maak een cloudwitness voor het failovercluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Maak een BLOB-container.

1. Sla de toegangs sleutels en de container-URL op.

1. Configureer de quorum-Witness van het failovercluster. Zie [de quorum-Witness configureren in de gebruikers interface](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) van de UI.


## <a name="step-4-test-cluster-failover"></a>Stap 4: failover van het cluster testen

Test de failover van het cluster. Klik in Failoverclusterbeheer met de rechter muisknop op uw cluster > **meer acties** > **basis Cluster resource verplaatsen** > **knoop punt selecteren** en selecteer het andere knoop punt van het cluster. Verplaats de kern cluster bron naar elk knoop punt van het cluster en verplaats deze vervolgens terug naar het primaire knoop punt. Als u het cluster naar elk knoop punt kunt verplaatsen, bent u klaar om SQL Server te installeren.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Cluster-Failover testen door de kern bron te verplaatsen naar de andere knoop punten":::

## <a name="step-5-create-sql-server-fci"></a>Stap 5: SQL Server FCI maken

Nadat u het failovercluster hebt geconfigureerd, kunt u de SQL Server FCI maken.

1. Maak verbinding met de eerste virtuele machine met RDP.

1. Controleer in **Failoverclusterbeheer**of alle kern bronnen van het cluster op de eerste virtuele machine staan. Verplaats, indien nodig, alle resources naar deze virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full`. Klik op **instellen**.

1. Klik in het **SQL Server-installatie centrum**op **installeren**.

1. Klik op **nieuw SQL Server failover-cluster installatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevens directory's moeten zich op de Premium-bestands share bestaan. Typ het volledige pad van de share in de vorm van `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Er wordt een waarschuwing weer gegeven waarin u wordt gewaarschuwd dat u een bestands server hebt opgegeven als de data directory. Dit is normaal gedrag. Zorg ervoor dat hetzelfde account dat u hebt opgeslagen, de bestands share is hetzelfde account dat door de SQL Server-service wordt gebruikt om mogelijke storingen te voor komen. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Bestands share gebruiken als SQL-gegevens mappen":::

1. Nadat u de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de installatie van de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met RDP.

1. Open het **SQL Server-installatie centrum**. Klik op **installatie**.

1. Klik op **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en deze server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt met SQL Server, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u deze installatie kopie niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Stap 6: Azure load balancer maken

Op virtuele machines van Azure gebruiken clusters een load balancer voor het opslaan van een IP-adres dat op één cluster knooppunt tegelijk moet zijn. In deze oplossing bevat de load balancer het IP-adres voor de SQL Server FCI.

[Een Azure-Load Balancer maken en configureren](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Maak de load balancer in het Azure Portal

De load balancer maken:

1. Ga in het Azure Portal naar de resource groep met de virtuele machines.

1. Klik op **+ Toevoegen**. Zoek op de Marketplace naar **Load Balancer**. Klik op **Load Balancer**.

1. Klik op **Maken**.

1. Configureer de load balancer met:

   - **Abonnement**: uw Azure-abonnement.
   - **Resource groep**: gebruik dezelfde resource groep als uw virtuele machines.
   - **Naam**: een naam die de Load Balancer aanduidt.
   - **Regio**: gebruik dezelfde Azure-locatie als uw virtuele machines.
   - **Type**: de Load Balancer kunnen openbaar of privé zijn. Een persoonlijke load balancer kan vanuit hetzelfde VNET worden geopend. De meeste Azure-toepassingen kunnen een privé-load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang moet hebben tot SQL Server, gebruik dan een open bare load balancer.
   - **SKU**: de SKU voor uw Load Balancer moet standaard zijn. 
   - **Virtual Network**: hetzelfde netwerk als de virtuele machines.
   - **IP-adres toewijzing**: de toewijzing van het IP-adres moet statisch zijn. 
   - **Privé-IP-adres**: hetzelfde IP-adres dat u hebt toegewezen aan de resource van het SQL Server FCI-cluster netwerk.
   Zie de volgende afbeelding:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>De load balancer back-end-groep configureren

1. Ga terug naar de Azure-resource groep met de virtuele machines en zoek de nieuwe load balancer. Mogelijk moet u de weer gave van de resource groep vernieuwen. Klik op het load balancer.

1. Klik op **back-end-Pools** en klik op **+ toevoegen** om een back-end-groep toe te voegen.

1. Koppel de back-end-pool met de beschikbaarheidsset die de virtuele machines bevat.

1. Onder **IP-configuraties**voor het doelnet werk controleert u de **virtuele machine** en kiest u de virtuele machines die als cluster knooppunten zullen worden beschouwd. Zorg ervoor dat u alle virtuele machines opneemt die als host moeten fungeren voor de FCI. 

1. Klik op **OK** om de back-end-pool te maken.

### <a name="configure-a-load-balancer-health-probe"></a>Een load balancer Health probe configureren

1. Klik op de Blade load balancer op **status controles**.

1. Klik op **+ Toevoegen**.

1. <a name="probe"> </a>Stel op de Blade **status test toevoegen** de para meters voor de status test in:

   - **Naam**: een naam voor de status test.
   - **Protocol**: TCP.
   - **Poort**: Stel in [deze stap](#ports)de poort in die u in de firewall hebt gemaakt voor de status test. In dit artikel maakt het voor beeld gebruik van TCP-poort `59999`.
   - **Interval**: 5 seconden.
   - **Drempel waarde voor onjuiste status**: 2 opeenvolgende fouten.

1. Klik op OK.

### <a name="set-load-balancing-rules"></a>Taakverdelings regels instellen

1. Klik op de Blade load balancer op **taakverdelings regels**.

1. Klik op **+ Toevoegen**.

1. De para meters voor de taakverdelings regels instellen:

   - **Naam**: een naam voor de regels voor taak verdeling.
   - **Frontend-IP-adres**: gebruik het IP-adres voor de resource van het SQL Server FCI-cluster netwerk.
   - **Poort**: ingesteld voor de SQL Server FCI TCP-poort. De standaard instantie poort is 1433.
   - **Backend-poort**: deze waarde gebruikt dezelfde poort als de **poort** waarde wanneer u **zwevende IP (direct server return)** inschakelt.
   - **Back-end-pool**: gebruik de naam van de back-endserver die u eerder hebt geconfigureerd.
   - **Status test**: gebruik de status test die u eerder hebt geconfigureerd.
   - **Sessie persistentie**: geen.
   - **Time-out voor inactiviteit (minuten)** : 4.
   - **Zwevend IP (direct server return)** : ingeschakeld

1. Klik op **OK**.

## <a name="step-7-configure-cluster-for-probe"></a>Stap 7: cluster configureren voor test

Stel de para meter cluster probe Port in Power shell in.

Als u de para meter voor de cluster test poort wilt instellen, moet u de variabelen in het volgende script bijwerken met waarden uit uw omgeving. Verwijder de punt haken `<>` uit het script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

In het voor gaande script stelt u de waarden in voor uw omgeving. In de volgende lijst worden de waarden beschreven:

   - `<Cluster Network Name>`: naam van Windows Server-failovercluster voor het netwerk. Klik in **Failoverclusterbeheer** > **netwerken**met de rechter muisknop op het netwerk en klik op **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** . 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP-adres resource naam. Klik in **Failoverclusterbeheer** > -**rollen**onder de rol SQL Server FCI onder **Server naam**met de rechter muisknop op de IP-adres bron en klik op **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** . 

   - `<ILBIP>`: het IP-adres van ILB. Dit adres wordt geconfigureerd in de Azure Portal als het front-end-adres van de ILB. Dit is ook het SQL Server IP-adres van FCI. U kunt deze in **Failoverclusterbeheer** vinden op dezelfde eigenschappen pagina waar u de `<SQL Server FCI IP Address Resource Name>` hebt gevonden.  

   - `<nnnnn>`: is de test poort die u hebt geconfigureerd in de load balancer-status test. Alle ongebruikte TCP-poort is geldig. 

>[!IMPORTANT]
>Het subnetmasker voor de cluster parameter moet het TCP IP-broadcast adres zijn: `255.255.255.255`.

Nadat u de cluster test hebt ingesteld, kunt u alle cluster parameters in Power shell zien. Voer het volgende script uit:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Stap 8: FCI failover testen

Test de failover van de FCI om de functionaliteit van het cluster te valideren. Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server FCI-cluster knooppunten met RDP.

1. Open **Failoverclusterbeheer**. Klik op **rollen**. U ziet welk knoop punt eigenaar is van de SQL Server rol FCI.

1. Klik met de rechter muisknop op de SQL Server FCI rol.

1. Klik op **verplaatsen** en klik vervolgens op **best mogelijke knoop punt**.

**Failoverclusterbeheer** toont de functie en de bijbehorende resources worden offline gezet. De resources worden vervolgens verplaatst en online gezet op het andere knoop punt.

### <a name="test-connectivity"></a>Connectiviteit testen

Als u de connectiviteit wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de naam van de SQL Server FCI.

>[!NOTE]
>Als dat nodig is, kunt u [SQL Server Management Studio downloaden](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Beperkingen

Azure Virtual Machines ondersteunt micro soft Distributed Transaction Coordinator (MSDTC) op Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en een [standaard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

Op virtuele machines van Azure wordt MSDTC niet ondersteund in Windows Server 2016 en lager omdat:

- De geclusterde MSDTC-bron kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u met Windows Server 2016 een MSDTC-bron maakt, wordt er geen gedeelde opslag weer gegeven die beschikbaar is voor gebruik, zelfs niet als de opslag ruimte is. Dit probleem is opgelost in Windows Server 2019.
- De Basic-load balancer verwerkt geen RPC-poorten.

## <a name="see-also"></a>Zie ook

- [Windows-cluster technologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server failover-cluster exemplaren](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).
