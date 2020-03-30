---
title: Hoge beschikbaarheid en disaster recovery voor SQL Server | Microsoft Documenten
description: Een bespreking van de verschillende soorten HADR-strategieën voor SQL Server die worden uitgevoerd in Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249761"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines

Microsoft Azure virtual machines (VM's) met SQL Server kunnen helpen de kosten van een hadr-databaseoplossing met hoge beschikbaarheid en noodherstel (HADR) te verlagen. De meeste SQL Server HADR-oplossingen worden ondersteund in virtuele Azure-machines, zowel als alleen Azure als als hybride oplossingen. In een Azure-oplossing wordt het hele HADR-systeem uitgevoerd in Azure. In een hybride configuratie wordt een deel van de oplossing uitgevoerd in Azure en het andere deel wordt on-premises uitgevoerd in uw organisatie. De flexibiliteit van de Azure-omgeving stelt u in staat om geheel of gedeeltelijk naar Azure te gaan om te voldoen aan de budget- en HADR-vereisten van uw SQL Server-databasesystemen.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Inzicht in de behoefte aan een HADR-oplossing
Het is aan u om ervoor te zorgen dat uw databasesysteem beschikt over de HADR-mogelijkheden die de service-level agreement (SLA) vereist. Het feit dat Azure mechanismen met hoge beschikbaarheid biedt, zoals servicehealing voor cloudservices en fouthersteldetectie voor virtuele machines, garandeert niet zelf dat u aan de gewenste SLA voldoen. Deze mechanismen beschermen de hoge beschikbaarheid van de VM's, maar niet de hoge beschikbaarheid van SQL Server die in de VM's wordt uitgevoerd. Het is mogelijk dat de SQL Server-instantie mislukt terwijl de VM online en gezond is. Bovendien zorgen zelfs de hoge beschikbaarheidsmechanismen van Azure voor downtime van de VM's als gevolg van gebeurtenissen zoals herstel van software- of hardwarefouten en upgrades van besturingssystemen.

Bovendien is Geo Redundant Storage (GRS) in Azure, dat wordt geïmplementeerd met een functie genaamd geo-replicatie, mogelijk geen adequate oplossing voor noodherstel voor uw databases. Omdat georeplicatie gegevens asynchroon verzendt, kunnen recente updates verloren gaan in geval van een ramp. Meer informatie over georeplicatiebeperkingen vindt u betrekking op de [georeplicatie die niet wordt ondersteund voor gegevens en logboekbestanden op afzonderlijke schijven.](#geo-replication-support)

## <a name="hadr-deployment-architectures"></a>HADR-implementatiearchitecturen
SQL Server HADR-technologieën die in Azure worden ondersteund, zijn onder andere:

* [Beschikbaarheidsgroepen altijd op beschikbaarheid](https://technet.microsoft.com/library/hh510230.aspx)
* [Altijd op failoverclusterexemplaren](https://technet.microsoft.com/library/ms189134.aspx)
* [Logboekverzending](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server-back-up en herstel met Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx)
* [Database mirroring](https://technet.microsoft.com/library/ms189852.aspx) - Afgeschaft in SQL Server 2016

Het is mogelijk om de technologieën samen te combineren om een SQL Server-oplossing te implementeren die zowel hoge beschikbaarheid als noodherstelmogelijkheden heeft. Afhankelijk van de technologie die u gebruikt, kan een hybride implementatie een VPN-tunnel met het virtuele Azure-netwerk vereisen. In de onderstaande secties ziet u enkele van de voorbeeldimplementatiearchitecturen.

## <a name="azure-only-high-availability-solutions"></a>Alleen Azure: oplossingen voor hoge beschikbaarheid

U een oplossing voor hoge beschikbaarheid voor SQL Server hebben op databaseniveau met Always On Availability Groups - beschikbaarheidsgroepen genoemd. U ook een oplossing met hoge beschikbaarheid maken op instantieniveau met Always On Failover Cluster Instances - failoverclusterexemplaren. Voor extra redundantie u redundantie op beide niveaus maken door beschikbaarheidsgroepen te maken voor failoverclusterexemplaren. 

| Technologie | Voorbeeldarchitecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Replica's voor beschikbaarheid die worden uitgevoerd in Azure VM's in dezelfde regio bieden een hoge beschikbaarheid.  U moet een VM voor domeincontroller configureren, omdat voor Windows-failoverclustering een Active Directory-domein vereist is.<br/><br/> Voor een hogere redundantie en beschikbaarheid kunnen de Azure VM's worden geïmplementeerd in verschillende [beschikbaarheidszones](../../../availability-zones/az-overview.md) zoals gedocumenteerd in het overzicht van de [beschikbaarheidsgroep.](virtual-machines-windows-portal-sql-availability-group-overview.md) Als de SQL Server VM's in een beschikbaarheidsgroep worden geïmplementeerd in beschikbaarheidszones, gebruikt u [standaard load balancer](../../../load-balancer/load-balancer-standard-overview.md) voor listener zoals gedocumenteerd in deze artikelen - [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure Quickstart-sjablonen](virtual-machines-windows-sql-availability-group-quickstart-template.md).<br/> ![Beschikbaarheidsgroepen](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Zie [Beschikbaarheidsgroepen configureren in Azure (GUI) voor](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)meer informatie. |
| **Failoverclusterexemplaren** |Failoverclusterinstanties (FCI), waarvoor gedeelde opslag vereist is, kunnen op 4 verschillende manieren worden gemaakt.<br/><br/>1. Een failovercluster met twee clients met twee nodes dat wordt uitgevoerd in Azure VM's met bijbehorende opslag met [Windows Server \(2016 Storage Spaces Direct S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) om een softwaregebaseerdvirtueel SAN te bieden.<br/><br/> 2. Een failovercluster met twee clients met twee clients die worden uitgevoerd in Azure VM's met Behulp van [Premium File Share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). Premium bestandsshares zijn ssd-backed consistent-low-latency file shares die volledig worden ondersteund voor gebruik met Failover Cluster Instance.<br/><br/>3. Een failovercluster met twee clients met twee clients die worden uitgevoerd in Azure VM's met opslag die wordt ondersteund door een clusteringoplossing van derden. Zie [Hoge beschikbaarheid voor een bestandsshare met failoverclustering en software van derden SIOS DataKeeper voor](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)een specifiek voorbeeld dat SIOS DataKeeper gebruikt.<br/><br/>4. Een failovercluster met twee clients met twee clients die worden uitgevoerd in Azure VM's met externe gedeelde blokopslag voor iSCSI Target via ExpressRoute. NetApp Private Storage (NPS) stelt bijvoorbeeld een iSCSI-doel via ExpressRoute met Equinix bloot aan Azure VM's.<br/><br/>Voor oplossingen voor gedeelde opslag en gegevensreplicatie van derden moet u contact opnemen met de leverancier voor problemen met betrekking tot de toegang tot gegevens bij failover.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Alleen Azure: oplossingen voor herstel na noodgevallen
U een oplossing voor noodherstel voor uw SQL Server-databases in Azure hebben met behulp van beschikbaarheidsgroepen, databasemirroring of back-up en herstel met opslagblobs.

| Technologie | Voorbeeldarchitecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Replica's voor beschikbaarheid die in meerdere datacenters in Azure VM's worden uitgevoerd voor herstel na noodgevallen. Deze oplossing voor verschillende regio's beschermt tegen volledige uitval van de site. <br/> ![Beschikbaarheidsgroepen](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Binnen een regio moeten alle replica's zich binnen dezelfde cloudservice en dezelfde VNet bevinden. Omdat elke regio een aparte VNet heeft, vereisen deze oplossingen VNet-connectiviteit met VNet. Zie [Een VNet-verbinding configureren met behulp van de Azure-portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)voor meer informatie. Zie [Een SQL Server-beschikbaarheidsgroep configureren op Azure Virtual Machines in verschillende regio's](virtual-machines-windows-portal-sql-availability-group-dr.md)voor gedetailleerde instructies.|
| **Database Mirroring** |Principal en mirror en servers die in verschillende datacenters draaien voor disaster recovery. U moet implementeren met servercertificaten. SQL Server-databasemirroring wordt niet ondersteund voor SQL Server 2008 of SQL Server 2008 R2 op een Azure VM. <br/>![Database Mirroring](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Back-up maken en herstellen met Azure Blob Storage Service** |Er wordt rechtstreeks een back-up van productiedatabases naar blobopslag in een ander datacenter voor herstel na noodgevallen.<br/>![Back-up en herstel](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Zie [Back-up en herstel voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-backup-recovery.md)meer informatie. |
| **SQL Server repliceren en mislukken naar Azure met Azure-siteherstel** |Productie SQL Server van één Azure-datacenter die rechtstreeks wordt gerepliceerd naar Azure Storage van verschillende Azure-datacenters voor herstel na noodgevallen.<br/>![Repliceren met Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server disaster recovery en Azure Site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybride IT: oplossingen voor noodherstel
U een oplossing voor noodherstel voor uw SQL Server-databases in een hybride-IT-omgeving hebben met behulp van beschikbaarheidsgroepen, databasemirroring, logboekverzending en back-up en herstel met Azure-blogopslag.

| Technologie | Voorbeeldarchitecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Sommige beschikbaarheidsreplica's die worden uitgevoerd in Azure VM's en andere replica's die on-premises worden uitgevoerd voor herstel van cross-site rampen. De productiesite kan on-premises of in een Azure-datacenter zijn.<br/>![Beschikbaarheidsgroepen](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Omdat alle beschikbaarheidsreplica's zich in hetzelfde failovercluster moeten bevinden, moet het cluster beide netwerken omvatten (een cluster met meerdere subnetfailover). Deze configuratie vereist een VPN-verbinding tussen Azure en het on-premises netwerk.<br/><br/>Voor een succesvol herstel na noodgevallen van uw databases moet u ook een replica-domeincontroller installeren op de site voor noodherstel.<br/><br/>Het is mogelijk om de wizard Replica toevoegen in SSMS te gebruiken om een Azure-replica toe te voegen aan een bestaande groep voor beschikbaarheid always on. Zie Zelfstudie: Uw groep Altijd op beschikbaarheid uitbreiden naar Azure voor meer informatie. |
| **Database Mirroring** |Een partner die wordt uitgevoerd in een Azure VM en de andere die on-premises wordt uitgevoerd voor cross-site disaster recovery met behulp van servercertificaten. Partners hoeven zich niet in hetzelfde Active Directory-domein te bevinden en er is geen VPN-verbinding vereist.<br/>![Database Mirroring](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Een ander scenario voor het spiegelen van de database omvat een partner die wordt uitgevoerd in een Azure VM en de andere die on-premises wordt uitgevoerd in hetzelfde Active Directory-domein voor herstel van cross-site-nood. Een [VPN-verbinding tussen het virtuele Azure-netwerk en het on-premises netwerk](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) is vereist.<br/><br/>Voor een succesvol herstel na noodgevallen van uw databases moet u ook een replica-domeincontroller installeren op de site voor noodherstel. SQL Server-databasemirroring wordt niet ondersteund voor SQL Server 2008 of SQL Server 2008 R2 op een Azure VM. |
| **Logboekverzending** |De ene server die wordt uitgevoerd in een Azure VM en de andere die on-premises wordt uitgevoerd voor herstel van cross-site rampen. Het verzenden van logboeken is afhankelijk van het delen van Windows-bestanden, dus een VPN-verbinding tussen het virtuele Azure-netwerk en het on-premises netwerk is vereist.<br/>![Logboekverzending](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Voor een succesvol herstel na noodgevallen van uw databases moet u ook een replica-domeincontroller installeren op de site voor noodherstel. |
| **Back-up maken en herstellen met Azure Blob Storage Service** |Er wordt rechtstreeks een back-up van on-premises productiedatabases naar Azure blob-opslag voor herstel na noodgevallen.<br/>![Back-up en herstel](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Zie [Back-up en herstel voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-backup-recovery.md)meer informatie. |
| **SQL Server repliceren en mislukken naar Azure met Azure-siteherstel** |On-premises productie SQL Server direct gerepliceerd naar Azure Storage voor disaster recovery.<br/>![Repliceren met Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server disaster recovery en Azure Site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |


## <a name="free-dr-replica-in-azure"></a>Gratis DR-replica in Azure

Als u [Software Assurance hebt,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)u DR-plannen (hybrid disaster recovery) implementeren met SQL Server zonder extra licentiekosten te maken voor het passieve DR-exemplaar.

In de onderstaande afbeelding wordt sql server gebruikt die wordt uitgevoerd op een Azure Virtual Machine met behulp van 12 cores als een disaster recovery-replica voor een on-premises SQL Server-implementatie met behulp van 12 cores. In het verleden moet u 12 cores sql server in licentie geven voor de on-premises en de Azure Virtual Machine-implementatie. Het nieuwe voordeel biedt passieve replicavoordelen die worden uitgevoerd op een Azure Virtual Machine. Nu hoeft u slechts 12 cores van SQL Server in licentie te geven die on-premises wordt uitgevoerd, zolang aan de criteria voor noodherstel voor de passieve replica op Azure Virtual Machine is voldaan.

![Gratis DR-replica in Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Zie de voorwaarden [voor productlicentieverlening](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie. 

Als u dit voordeel wilt inschakelen, navigeert u naar de [virtuele machinebron van SQL Server,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)selecteert u **Configureren** onder instellingen en kiest u de optie **Rampherstel** onder **SQL Server-licentie.** Schakel het selectievakje in om te controleren of deze SQL Server VM wordt gebruikt als passieve replica en selecteer **Toepassen** om uw instellingen op te slaan. 

![DR-replica configureren in Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Belangrijke overwegingen voor SQL Server HADR in Azure
Azure VM's, opslag en netwerken hebben andere operationele kenmerken dan een on-premises, niet-gevirtualiseerde IT-infrastructuur. Een succesvolle implementatie van een HADR SQL Server-oplossing in Azure vereist dat u deze verschillen begrijpt en uw oplossing ontwerpt om ze aan te passen.

### <a name="high-availability-nodes-in-an-availability-set"></a>Knooppunten met hoge beschikbaarheid in een beschikbaarheidsset
Met beschikbaarheidssets in Azure u de knooppunten met hoge beschikbaarheid plaatsen in afzonderlijke foutdomeinen (FD's) en UDs (Update Domains). Elke virtuele machine in uw beschikbaarheidsset krijgt een updatedomein en een foutdomein toegewezen door het onderliggende Azure-platform. Deze configuratie binnen een datacenter zorgt ervoor dat tijdens een geplande of ongeplande onderhoudsgebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de 99,95% Azure SLA. Als u de installatie met hoge beschikbaarheid wilt configureren, plaatst u alle deelnemende SQL Virtual Machines in dezelfde beschikbaarheidsset om toepassings- of gegevensverlies tijdens een onderhoudsgebeurtenis te voorkomen. Alleen knooppunten in dezelfde cloudservice kunnen deelnemen aan dezelfde beschikbaarheidsset. Zie [De beschikbaarheid van virtuele machines beheren](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie .

### <a name="high-availability-nodes-in-an-availability-zone"></a>Knooppunten met hoge beschikbaarheid in een beschikbaarheidszone
Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. De fysieke scheiding van beschikbaarheidszones binnen een regio beschermt toepassingen en gegevens tegen datacenterfouten door ervoor te zorgen dat ten minste één virtuele machine beschikbaar is en voldoet aan 99,99% Azure SLA. Als u een hoge beschikbaarheid wilt configureren, plaatst u deelnemende SQL Virtual Machines verspreid over beschikbare beschikbaarheidszones in de regio. Er zullen extra kosten voor de vm-to-VM-gegevensoverdracht tussen de beschikbaarheidszone en de VM zijn. Zie [Beschikbaarheidszones voor](/azure/availability-zones/az-overview)meer informatie . 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Failoverclustergedrag in Azure-netwerken
De dhcp-service die niet compatibel is met RFC in Azure kan ertoe leiden dat bepaalde failoverclusterconfiguraties mislukken, omdat de naam van het clusternetwerk een duplicaat IP-adres krijgt toegewezen, zoals hetzelfde IP-adres als een van de clusterknooppunten. Dit is een probleem wanneer u Beschikbaarheidsgroepen implementeert, wat afhankelijk is van de windows-failoverclusterfunctie.

Houd rekening met het scenario waarin een cluster met twee plaatsen wordt gemaakt en online wordt gebracht:

1. Het cluster komt online, vervolgens vraagt NODE1 een dynamisch toegewezen IP-adres aan voor de naam van het clusternetwerk.
2. Geen enkel IP-adres dan het eigen IP-adres van NODE1 wordt door de DHCP-service opgegeven, omdat de DHCP-service erkent dat de aanvraag afkomstig is van NODE1 zelf.
3. Windows detecteert dat een duplicaatadres is toegewezen aan NODE1 en aan de naam van het failoverclusternetwerk en dat de standaardclustergroep niet online komt.
4. De standaardclustergroep wordt verplaatst naar NODE2, waarmee het IP-adres van NODE1 wordt behandeld als het ip-adres van het cluster en de standaardclustergroep online wordt geplaatst.
5. Wanneer NODE2 probeert verbinding te maken met NODE1, verlaten pakketten die op NODE1 zijn gericht, NODE2 nooit omdat het IP-adres van NODE1 voor zichzelf wordt opgelost. NODE2 kan geen verbinding met NODE1 tot stand brengen, verliest vervolgens quorum en sluit het cluster af.
6. In de tussentijd kan NODE1 pakketten naar NODE2 sturen, maar NODE2 kan niet reageren. NODE1 verliest quorum en sluit het cluster af.

Dit scenario kan worden vermeden door een ongebruikt statisch IP-adres, zoals een linklokaal IP-adres zoals 169.254.1.1, toe te schrijven aan de naam van het clusternetwerk om de naam van het clusternetwerk online te brengen. Zie [Windows-failovercluster configureren in Azure voor beschikbaarheidsgroepen](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)om dit proces te vereenvoudigen.

Zie [Beschikbaarheidsgroepen configureren in Azure (GUI) voor](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)meer informatie.

### <a name="availability-group-listener-support"></a>Ondersteuning voor de listener van de beschikbaarheidsgroep
De groeplisteners voor beschikbaarheid worden ondersteund op Azure VM's met Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 en Windows Server 2016. Deze ondersteuning wordt mogelijk gemaakt door het gebruik van load-balanced endpoints die zijn ingeschakeld op de Azure VM's die beschikbaarheidsgroepknooppunten zijn. U moet speciale configuratiestappen volgen zodat de listeners kunnen werken voor zowel clienttoepassingen die in Azure worden uitgevoerd als clients die on-premises worden uitgevoerd.

Er zijn twee belangrijke opties voor het instellen van uw luisteraar: extern (openbaar) of intern. De externe (publieke) luisteraar maakt gebruik van een internet facing load balancer en wordt geassocieerd met een openbare Virtual IP (VIP) die toegankelijk is via het internet. Een interne listener gebruikt een interne load balancer en ondersteunt alleen clients binnen hetzelfde virtuele netwerk. Voor een type load balancer moet u Direct Server Return inschakelen. 

Als de beschikbaarheidsgroep meerdere Azure-subnetten omvat (zoals een implementatie die Azure-regio's kruist), moet de tekenreeks clientverbinding "**MultisubnetFailover=True "** bevatten. Dit resulteert in parallelle verbindingspogingen met de replica's in de verschillende subnetten. Zie voor instructies over het instellen van een listener

* [Een ILB-listener configureren voor beschikbaarheidsgroepen in Azure.](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)
* [Een externe listener configureren voor beschikbaarheidsgroepen in Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

U nog steeds afzonderlijk verbinding maken met elke beschikbaarheidsreplica door rechtstreeks verbinding te maken met de service-instantie. Aangezien beschikbaarheidsgroepen achterwaarts compatibel zijn met databasemirroringclients, u ook verbinding maken met de beschikbaarheidsreplica's zoals databasemirroring-partners, zolang de replica's zijn geconfigureerd, vergelijkbaar met databasemirroring:

* Eén primaire replica en één secundaire replica
* De secundaire replica is geconfigureerd als niet-leesbaar (**Leesbare secundaire** optie ingesteld op **Nee)**

Hieronder vindt u een voorbeeldvan de tekenreeks clientverbinding die overeenkomt met deze databasespiegeling-achtige configuratie met behulp van ADO.NET of SQL Server Native Client:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Zie voor meer informatie over clientconnectiviteit:

* [Trefwoorden voor verbindingstekenreeks gebruiken met SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Clients verbinden met een Database Mirroring Session (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Verbinding maken met Availability Group Listener in Hybrid IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Beschikbaarheidsgroeplisteners, clientconnectiviteit en failover van toepassingen (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Database-mirroring-verbindingstekenreeksen gebruiken met beschikbaarheidsgroepen](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Netwerklatentie in hybride IT
U moet uw HADR-oplossing implementeren in de veronderstelling dat er perioden kunnen zijn met een hoge netwerklatentie tussen uw on-premises netwerk en Azure. Wanneer u replica's implementeert naar Azure, moet u asynchrone commit gebruiken in plaats van synchrone commit voor de synchronisatiemodus. Gebruik bij het implementeren van databasemirroringservers zowel on-premises als in Azure de krachtige modus in plaats van de high-safety-modus.

### <a name="geo-replication-support"></a>Ondersteuning voor georeplicatie
Georeplicatie in Azure-schijven biedt geen ondersteuning voor het gegevensbestand en logboekbestand van dezelfde database dat op afzonderlijke schijven moet worden opgeslagen. GRS repliceert wijzigingen op elke schijf onafhankelijk en asynchroon. Dit mechanisme garandeert de schrijfvolgorde binnen één schijf op de geo-gerepliceerde kopie, maar niet over geo-gerepliceerde kopieën van meerdere schijven. Als u een database configureert om het gegevensbestand en het logboekbestand op afzonderlijke schijven op te slaan, kunnen de herstelde schijven na een ramp een meer up-to-date kopie van het gegevensbestand bevatten dan het logboekbestand, dat het voorgeschreven logboek in SQL Server en de ACID-eigenschappen van Transacties. Als u niet de optie hebt om georeplicatie op het opslagaccount uit te schakelen, moet u alle gegevens en logboekbestanden voor een bepaalde database op dezelfde schijf bewaren. Als u meer dan één schijf moet gebruiken vanwege de grootte van de database, moet u een van de hierboven genoemde oplossingen voor noodherstel implementeren om gegevensredundantie te garanderen.

## <a name="next-steps"></a>Volgende stappen
Zie Een SQL Server Virtual Machine op [Azure inrichten als](virtual-machines-windows-portal-sql-server-provision.md)u een virtuele Azure-machine met SQL Server wilt maken.

Zie de richtlijnen voor Aanbevolen procedures voor SQL Server [in Azure Virtual Machines](virtual-machines-windows-sql-performance.md)om de beste prestaties van SQL Server op een Azure VM te krijgen.

Zie [SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's.

### <a name="other-resources"></a>Meer informatie
* [Een nieuw Active Directory-forest installeren in Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Failovercluster maken voor beschikbaarheidsgroepen in Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

