---
title: SQL Server FCI met premium bestandsshare - Azure Virtual Machines
description: In dit artikel wordt uitgelegd hoe u een SQL Server-failoverclusterinstantie maakt met behulp van een premium bestandsshare op virtuele Azure-machines.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303219"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Een SQL Server-failoverclusterexemplaar configureren met premium bestandsshare op virtuele Azure-machines

In dit artikel wordt uitgelegd hoe u een SQL Server failoverclusterinstantie (FCI) maakt op virtuele Azure-machines met behulp van een [premium bestandsshare](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Premium bestandsshares zijn met SSD-ondersteunde, consistent low-latency bestandsshares die volledig worden ondersteund voor gebruik met Failover Cluster Instances voor SQL Server 2012 of hoger op Windows Server 2012 of hoger. Premium bestandsshares bieden u meer flexibiliteit, zodat u het formaat en de schaal van een bestandsshare wijzigen zonder downtime.


## <a name="before-you-begin"></a>Voordat u begint

Er zijn een paar dingen die je moet weten en hebben in plaats voordat je begint.

U moet een operationeel inzicht hebben in deze technologieën:

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failoverclusterexemplaren](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Een ding om bewust te zijn van is dat op een Azure IaaS VM failover cluster, raden we een enkele NIC per server (cluster knooppunt) en een enkel subnet. Azure-netwerken hebben fysieke redundantie waardoor extra NIC's en subnetten overbodig worden op een Azure IaaS VM-gastcluster. In het clustervalidatierapport wordt u gewaarschuwd dat de knooppunten slechts op één netwerk bereikbaar zijn. U deze waarschuwing negeren op Azure IaaS VM-failoverclusters.

U moet ook een algemeen begrip van deze technologieën:

- [Azure premium bestandsshare](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-brongroepen](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Op dit moment worden SQL Server failoverclusterexemplaren op virtuele Azure-machines alleen ondersteund met de [lichtgewicht beheermodus](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) van de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md) Als u wilt overstappen van de volledige extensiemodus naar lichtgewicht, verwijdert u de **SQL Virtual Machine-bron** voor de bijbehorende VM's en registreert u deze vervolgens bij de SQL VM-resourceprovider in de lichtgewicht modus. Wanneer u de **SQL Virtual Machine-bron** met de Azure-portal verwijderd, **schakelt u het selectievakje uit naast de juiste virtuele machine**. De volledige extensie ondersteunt functies zoals geautomatiseerde back-up, patching en geavanceerd portalbeheer. Deze functies werken niet voor SQL VM's nadat de agent opnieuw is geïnstalleerd in de lichtgewicht beheermodus.

Premium bestandsshares bieden IOPS en in capaciteiten die voldoen aan de behoeften van veel workloads. Voor IO-intensieve workloads u SQL [Server Failover-clusterinstanties met Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md)overwegen op basis van beheerde premiumschijven of ultraschijven.  

Controleer de IOPS-activiteit van uw omgeving en controleer of premium bestandsshares de IOPS bieden die u nodig hebt voordat u een implementatie of migratie start. Gebruik schijftellers van Windows Prestatiemeter om de totale IOPS (Schijfoverdrachten per seconde) en doorvoer (Schijfbytes/seconde) te controleren die vereist zijn voor SQL Server-gegevens-, logboek- en Temp DB-bestanden.

Veel workloads hebben barstenIO, dus het is een goed idee om te controleren tijdens zware gebruiksperioden en let op zowel de maximale IOPS en de gemiddelde IOPS. Premium bestandsaandelen bieden IOPS op basis van de grootte van het aandeel. Premium bestandsaandelen bieden ook gratis barsten waarmee u uw IO barsten om het basislijnbedrag voor maximaal een uur te verdrievoudigen.

Zie [Prestatieniveaus voor bestandsshare](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)voor meer informatie over de prestaties van het aandeel van het eerste deel .

### <a name="licensing-and-pricing"></a>Licenties en prijzen

Op virtuele Azure-machines u SQL Server in licentie geven met pay-as-you-go (PAYG) of BYOL-vm-afbeeldingen (bring-your-own-license). Het type afbeelding dat u kiest, is van invloed op de manier waarop u wordt in rekening gebracht.

Met pay-as-you-go-licenties brengt een failoverclusterinstantie (FCI) van SQL Server op Azure virtuele machines kosten met zich mee voor alle knooppunten van de FCI, inclusief de passieve knooppunten. Zie [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)voor meer informatie.

Als u een Enterprise Agreement with Software Assurance hebt, u één gratis passief FCI-knooppunt gebruiken voor elk actief knooppunt. Als u wilt profiteren van dit voordeel in Azure, gebruikt u BYOL VM-afbeeldingen en gebruikt u dezelfde licentie op zowel de actieve als passieve knooppunten van de FCI. Zie [Ondernemingsovereenkomst](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)voor meer informatie .

Zie [Aan de slag met SQL VM's](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)voor het vergelijken van pay-as-you-go- en BYOL-licenties voor SQL Server op virtuele Azure-machines.

Zie [Prijzen](https://www.microsoft.com/sql-server/sql-server-2017-pricing)voor volledige informatie over het verlenen van licenties voor SQL Server.

### <a name="filestream"></a>Bestandsstroom

Filestream wordt niet ondersteund voor een failovercluster met een premium bestandsshare. Als u bestandsstream wilt gebruiken, implementeert u uw cluster met [Behulp van Direct opgeslagen ruimten](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel voltooit, moet u al het volgende hebben:

- Een Microsoft Azure-abonnement.
- Een Windows-domein op virtuele Azure-machines.
- Een domeingebruikersaccount met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- Een domeingebruikersaccount om de SQL Server-service uit te voeren en waarmee u zich bij het monteren van de bestandsshare bij de virtuele machine aanmelden.  
- Een virtueel Azure-netwerk en subnet met voldoende IP-adresruimte voor deze componenten:
   - Twee virtuele machines.
   - Het IP-adres van failovercluster.
   - Een IP-adres voor elke FCI.
- DNS geconfigureerd op het Azure-netwerk en wijst naar de domeincontrollers.
- Een [premium bestandsshare](../../../storage/files/storage-how-to-create-premium-fileshare.md) dat moet worden gebruikt als het geclusterde station, op basis van het opslagquotum van uw database voor uw gegevensbestanden.
- Als u windows server 2012 R2 en ouder gebruikt, hebt u een ander bestandsaandeel nodig om te gebruiken als getuige voor het delen van bestanden, omdat cloudgetuigen worden ondersteund voor Windows 2016 en nieuwer. U een andere Azure-bestandsshare gebruiken of u een bestandsshare gebruiken op een afzonderlijke virtuele machine. Als u een ander Azure-bestandsshare gaat gebruiken, u deze opnieuw gebruiken met hetzelfde proces als voor het premium bestandsaandeel dat wordt gebruikt voor uw geclusterde schijf. 

Met deze vereisten u beginnen met het bouwen van uw failovercluster. De eerste stap is het creëren van de virtuele machines.

## <a name="step-1-create-the-virtual-machines"></a>Stap 1: De virtuele machines maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw abonnement.

1. [Maak een Azure-beschikbaarheidsset](../tutorial-availability-sets.md).

   De beschikbaarheidsset groepeert virtuele machines tussen foutdomeinen en updatedomeinen. Het zorgt ervoor dat uw toepassing niet wordt beïnvloed door single points of failure, zoals de netwerkswitch of de power unit van een rack van servers.

   Als u de resourcegroep voor uw virtuele machines niet hebt gemaakt, doet u dit wanneer u een Azure-beschikbaarheidsset maakt. Als u de Azure-portal gebruikt om de beschikbaarheidsset te maken, gaat u de volgende stappen uitvoeren:

   1. Selecteer in de Azure-portal de optie **Een resource maken** om Azure Marketplace te openen. Zoeken naar **beschikbaarheidingesteld**.
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

   >[!IMPORTANT]
   > Nadat u de virtuele machine hebt gemaakt, verwijdert u de vooraf geïnstalleerde zelfstandige SQL Server-instantie. U gebruikt de vooraf geïnstalleerde SQL Server-media om de SQL Server FCI te maken nadat u het failovercluster en het premium bestandsshare hebt ingesteld als opslag.

   U ook Azure Marketplace-afbeeldingen gebruiken die alleen het besturingssysteem bevatten. Kies een **Datacenterafbeelding van Windows Server 2016** en installeer de SQL Server FCI nadat u het failovercluster en het premium bestandsshare hebt ingesteld als opslag. Deze afbeelding bevat geen SQL Server-installatiemedia. Plaats de SQL Server-installatiemedia op een locatie waar u het voor elke server uitvoeren.

1. Nadat Azure uw virtuele machines hebt gemaakt, maakt u verbinding met elk van deze machines met RDP.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met rdp, wordt u gevraagd of u wilt toestaan dat de pc in het netwerk kan worden ontdekt. Selecteer **Ja**.

1. Als u een van de op SQL Server gebaseerde virtuele machineafbeeldingen gebruikt, verwijdert u de SQL Server-instantie.

   1. Klik in **Programma's en onderdelen**met de rechtermuisknop op **Microsoft SQL Server 201_ (64-bits)** en selecteer **Verwijderen/wijzigen**.
   1. Selecteer **Verwijderen**.
   1. Selecteer de standaardinstantie.
   1. Verwijder alle functies onder **Database Engine Services**. Verwijder gedeelde **functies**niet . Je ziet iets als de volgende screenshot:

        ![Functies selecteren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecteer **Volgende**en selecteer **Verwijderen**.

1. <span id="ports"> </span> Open de firewallpoorten.  

   Open deze poorten op elke virtuele machine op de Windows Firewall:

   | Doel | TCP-poort | Opmerkingen
   | ------ | ------ | ------
   | SQL Server | 1433 | Normale poort voor standaardexemplaren van SQL Server. Als u een afbeelding uit de galerie hebt gebruikt, wordt deze poort automatisch geopend.
   | Statustest | 59999 | Elke open TCP-poort. Configureer in een latere stap de [statussprobe](#probe) van de load balancer en het cluster om deze poort te gebruiken.
   | Bestandsshare | 445 | Poort die wordt gebruikt door de bestandsshareservice.

1. [Voeg de virtuele machines toe aan uw reeds bestaande domein.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

Nadat u de virtuele machines hebt gemaakt en geconfigureerd, u het premium bestandsaandeel configureren.

## <a name="step-2-mount-the-premium-file-share"></a>Stap 2: Het premium bestandsaandeel monteren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw opslagaccount.
1. Ga naar **Bestandsshares** onder **Bestandsservice** en selecteer het premium bestandsaandeel dat u wilt gebruiken voor uw SQL-opslag.
1. Selecteer **Verbinding maken** om de verbindingstekenreeks voor uw bestandsshare weer te geven.
1. Selecteer de stationsletter die u wilt gebruiken in de vervolgkeuzelijst en kopieer beide codeblokken naar Kladblok.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Beide PowerShell-opdrachten kopiëren vanuit de verbindingsportal voor bestandsshare":::

1. Gebruik RDP om verbinding te maken met de SQL Server VM met het account dat uw SQL Server FCI voor het serviceaccount gebruikt.
1. Open een administratieve PowerShell-opdrachtconsole.
1. Voer de opdrachten uit die u eerder hebt opgeslagen toen u in de portal werkte.
1. Ga naar de share met Behulp van Verkenner of het dialoogvenster **Uitvoeren** (Windows-logotoets + r). Gebruik het `\\storageaccountname.file.core.windows.net\filesharename`netwerkpad . Bijvoorbeeld: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Maak ten minste één map op de nieuw verbonden bestandsshare om uw SQL-gegevensbestanden in te plaatsen.
1. Herhaal deze stappen op elke SQL Server VM die aan het cluster deelneemt.

  > [!IMPORTANT]
  > - Overweeg een afzonderlijke bestandsshare te gebruiken voor back-upbestanden om de IOPS en de ruimtecapaciteit van dit aandeel op te slaan voor gegevens- en logboekbestanden. U een premium of standaard bestandsshare gebruiken voor back-upbestanden.
  > - Als u Windows 2012 R2 en ouder gebruikt, volgt u dezelfde stappen om uw bestandsshare te monteren die u gaat gebruiken als getuige voor bestandsshare. 

## <a name="step-3-configure-the-failover-cluster"></a>Stap 3: Het failovercluster configureren

De volgende stap is het configureren van het failovercluster. In deze stap voltooit u de volgende substappen:

1. Voeg de functie Failoverclustering van Windows Server toe.
1. Valideer het cluster.
1. Maak het failovercluster.
1. Maak de cloudgetuige (voor Windows Server 2016 en nieuwer) of de getuige voor bestandsshare (voor Windows Server 2012 R2 en ouder).


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

### <a name="validate-the-cluster"></a>Het cluster valideren

Valideer het cluster in de gebruikersinterface of met PowerShell.

Als u het cluster wilt valideren met de gebruikersinterface, neemt u de volgende stappen op een van de virtuele machines:

1. Selecteer onder **Serverbeheer**De optie **Extra**en selecteer **Vervolgens Failoverclusterbeheer**.
1. Selecteer **onder Failoverclusterbeheer** **actie**en selecteer Vervolgens **Configuratie valideren**.
1. Selecteer **Volgende**.
1. Voer **onder Servers of een cluster selecteren**de namen van beide virtuele machines in.
1. Selecteer **Onder Testopties**de optie **Alleen tests uitvoeren die ik selecteer**. Selecteer **Volgende**.
1. Selecteer **onder Testselectie**alle tests, behalve **opslag-** en **opslagruimten direct,** zoals hier wordt weergegeven:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Clustervalidatietests selecteren":::

1. Selecteer **Volgende**.
1. Selecteer **Volgende**onder **Bevestiging**.

Met de **wizard Een configuratie valideren** worden de validatietests uitgevoerd.

Als u het cluster wilt valideren met PowerShell, voert u het volgende script uit vanuit een PowerShell-sessie van een beheerder op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.

### <a name="create-the-failover-cluster"></a>Het failovercluster maken

Als u het failovercluster wilt maken, moet u het sein
- De namen van de virtuele machines die de clusterknooppunten worden.
- Een naam voor het failovercluster
- Een IP-adres voor het failovercluster. U een IP-adres gebruiken dat niet wordt gebruikt op hetzelfde virtuele Azure-netwerk en subnet als de clusterknooppunten.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 tot en met Windows Server 2016

Met het volgende PowerShell-script wordt een failovercluster voor Windows Server 2012 gemaakt via Windows Server 2016. Werk het script bij met de namen van de knooppunten (de namen van de virtuele machine) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Met het volgende PowerShell-script wordt een failovercluster voor Windows Server 2019 gemaakt. Zie [Failovercluster: Clusternetwerkobject voor](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)meer informatie. Werk het script bij met de namen van de knooppunten (de namen van de virtuele machine) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Een cloudgetuige maken (Win 2016 +)

Als u windows server 2016 en hoger bevindt, moet u een cloudgetuige maken. Cloud Witness is een nieuw type clusterquorumgetuige dat is opgeslagen in een Azure-opslagblob. Hierdoor hoeft u geen afzonderlijke VM meer nodig te hebben die een getuigenaandeel host of een afzonderlijke bestandsshare gebruikt.

1. [Maak een cloudgetuige voor het failovercluster.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Maak een blobcontainer.

1. Sla de toegangssleutels en de URL van de container op.

### <a name="configure-quorum"></a>Quorum configureren 

Configureer voor Windows Server 2016 en hoger het cluster om de zojuist gemaakte cloudgetuige te gebruiken. Volg alle stappen [Configureer de quorumgetuige in de gebruikersinterface](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Volg voor Windows Server 2012 R2 en ouder dezelfde stappen in [De quorumgetuige configureren in de gebruikersinterface,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) maar selecteer op de pagina **Quorumgetuige selecteren** de optie Getuige configureren van **een bestandsshare.For** Windows Server 2012 R2 and older, do the same steps in Configure the quorum witness in the user interface but in the Select Quorum Witness page, select the Configure a file share witness option. Geef het bestandsaandeel op dat u hebt toegewezen als getuige voor bestandsshare, of het nu gaat om een afbeelding die u op een afzonderlijke virtuele machine hebt geconfigureerd of vanuit Azure bent gemonteerd. 


## <a name="step-4-test-cluster-failover"></a>Stap 4: Failover van het testcluster

Test failover van uw cluster. Klik in **Failoverclusterbeheer**met de rechtermuisknop op het cluster en selecteer Het**knooppunt Clusterbron Selecteer** **Meer acties** > **Verplaatsen Kernclusterbron** > selecteren en selecteer vervolgens het andere knooppunt van het cluster. Verplaats de kernclusterbron naar elk knooppunt van het cluster en verplaats deze vervolgens terug naar het primaire knooppunt. Als u het cluster naar elk knooppunt verplaatsen, u SQL Server installeren.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Clusterfailover testen door de kernbron naar de andere knooppunten te verplaatsen":::

## <a name="step-5-create-the-sql-server-fci"></a>Stap 5: De SQL Server FCI maken

Nadat u het failovercluster hebt geconfigureerd, u het SQL Server FCI maken.

1. Maak verbinding met de eerste virtuele machine via RDP.

1. Controleer in **Failoverclusterbeheer**of alle kernclusterbronnen zich op de eerste virtuele machine bevinden. Als het nodig is, verplaats alle resources naar deze virtuele machine.

1. Zoek de installatiemedia. Als de virtuele machine een van de Azure Marketplace-afbeeldingen gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full`. Selecteer **Setup**.

1. Selecteer **Installatie**in het **SQL Server-installatiecentrum**.

1. Selecteer **Nieuwe SQL Server-failoverclusterinstallatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI data directories moeten op de premium file share. Voer het volledige pad van het `\\storageaccountname.file.core.windows.net\filesharename\foldername`aandeel in dit formulier in: . Er verschijnt een waarschuwing waarin staat dat u een bestandsserver hebt opgegeven als gegevensmap. Deze waarschuwing wordt verwacht. Controleer of het gebruikersaccount waarmee u rdp'd in de VM terecht bent gegaan toen u het bestandsaandeel voortduurde, hetzelfde account is dat de SQL Server-service gebruikt om mogelijke fouten te voorkomen.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Bestandsshare gebruiken als SQL-gegevensmappen":::

1. Nadat u de stappen in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knooppunt.

1. Nadat Setup de FCI op het eerste knooppunt hebt geïnstalleerd, maakt u verbinding met het tweede knooppunt met RDP.

1. Open het **SQL Server Installation Center**. Selecteer **Installatie**.

1. Selecteer **Knooppunt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en voeg de server toe aan de FCI.

   >[!NOTE]
   >Als u een Azure Marketplace-galerieafbeelding met SQL Server hebt gebruikt, zijn SQL Server-hulpprogramma's bij de afbeelding opgenomen. Als u een van deze afbeeldingen niet hebt gebruikt, installeert u de SQL Server-hulpprogramma's afzonderlijk. Zie [SQL Server Management Studio (SSMS) downloaden.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="step-6-create-the-azure-load-balancer"></a>Stap 6: De Azure load balancer maken

Op virtuele Azure-machines gebruiken clusters een load balancer om een IP-adres vast te houden dat op één clusterknooppunt tegelijk moet staan. In deze oplossing houdt de load balancer het IP-adres voor de SQL Server FCI.

Zie [Een Azure load balancer maken en configureren](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)voor meer informatie.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>De load balancer maken in de Azure-portal

Ga als u de load balancer maken:

1. Ga in de Azure-portal naar de resourcegroep die de virtuele machines bevat.

1. Selecteer **Toevoegen**. Zoek in de Azure Marketplace voor **Load Balancer**. Selecteer **Load Balancer**.

1. Selecteer **Maken**.

1. Stel de load balancer in met de volgende waarden:

   - **Abonnement:** uw Azure-abonnement.
   - **Resourcegroep:** de resourcegroep die uw virtuele machines bevat.
   - **Naam:** een naam die de load balancer identificeert.
   - **Regio**: De Azure-locatie die uw virtuele machines bevat.
   - **Type**: Openbaar of privé. Een private load balancer is toegankelijk vanuit het virtuele netwerk. De meeste Azure-toepassingen kunnen een private load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang tot SQL Server nodig heeft, gebruikt u een public load balancer.
   - **SKU**: Standaard.
   - **Virtueel netwerk**: Hetzelfde netwerk als de virtuele machines.
   - **IP-adrestoewijzing**: Statisch. 
   - **Privé-IP-adres:** het IP-adres dat u hebt toegewezen aan de SQL Server FCI-clusternetwerkbron.

   In de volgende afbeelding ziet u de **gebruikersinterface van de load balancer** maken:

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

1. Stel op het **sondeblad Gezondheid toevoegen** de volgende parameters voor de statussonde in. <span id="probe"> </span>

   - **Naam:** Een naam voor de statussonde.
   - **Protocol**: TCP.
   - **Poort:** de poort die u in de firewall voor de statussonde in [deze stap](#ports)hebt gemaakt. In dit artikel wordt in `59999`het voorbeeld de TCP-poort gebruikt.
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Stap 7: Het cluster configureren voor de sonde

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

## <a name="step-8-test-fci-failover"></a>Stap 8: Test FCI failover

Test failover van de FCI om clusterfunctionaliteit te valideren. Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server FCI-clusterknooppunten met RDP.

1. **Failoverclusterbeheer openen**. Selecteer **Rollen**. Merk op welk knooppunt eigenaar is van de SQL Server FCI-rol.

1. Klik met de rechtermuisknop op de SQL Server FCI-rol.

1. Selecteer **Verplaatsen**en selecteer vervolgens **Het best mogelijke knooppunt**.

**Failoverclusterbeheer** toont de rol en de bronnen gaan offline. De middelen dan verplaatsen en weer online komen in de andere knooppunt.

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

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failoverclusterexemplaren](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
