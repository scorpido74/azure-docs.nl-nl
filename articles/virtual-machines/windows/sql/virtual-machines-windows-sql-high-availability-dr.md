---
title: Hoge Beschik baarheid en herstel na nood gevallen voor SQL Server | Microsoft Docs
description: Een bespreking van de verschillende soorten HADR-strategieën voor SQL Server die worden uitgevoerd in azure Virtual Machines.
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
ms.openlocfilehash: 1d0bdfbbad7e811ac8f1eeffb1991cc5430483a6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262896"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Maximale beschikbaarheid en herstel na noodgeval voor SQL Server in SQL Server Virtual Machine

Microsoft Azure virtuele machines (Vm's) met SQL Server kunnen de kosten van een Data Base-oplossing met hoge Beschik baarheid en herstel na nood gevallen (HADR) verlagen. De meeste SQL Server HADR-oplossingen worden ondersteund in virtuele machines van Azure, zowel als Azure-only als hybride oplossingen. In een alleen-Azure-oplossing wordt het hele HADR-systeem uitgevoerd in Azure. In een hybride configuratie wordt een deel van de oplossing uitgevoerd in Azure en wordt het andere onderdeel on-premises in uw organisatie uitgevoerd. Dankzij de flexibiliteit van de Azure-omgeving kunt u zich gedeeltelijk of volledig verplaatsen naar Azure om te voldoen aan de budget-en HADR vereisten van uw SQL Server-database systemen.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Meer informatie over de nood zaak van een HADR-oplossing
Het is aan u om ervoor te zorgen dat uw database systeem beschikt over de HADR-mogelijkheden die de Service Level Agreement (SLA) nodig heeft. Het feit dat Azure voorziet in een hoge beschik baarheids mechanismen, zoals service retoucheert voor Cloud Services en detectie van fout herstel voor Virtual Machines, garandeert niet dat u aan de gewenste SLA kunt voldoen. Deze mechanismen beveiligen de hoge Beschik baarheid van de virtuele machines, maar niet de hoge Beschik baarheid van SQL Server die in de Vm's worden uitgevoerd. Het is mogelijk dat het SQL Server-exemplaar mislukt als de virtuele machine online en in orde is. Daarnaast bieden de mechanismen voor hoge Beschik baarheid die door Azure worden ondersteund, de uitval tijd van de Vm's mogelijk vanwege gebeurtenissen zoals herstel van software-of hardwarestoringen en upgrades van besturings systemen.

Daarnaast is geografisch redundante opslag (GRS) in azure, dat is geïmplementeerd met een functie met de naam Geo-replicatie, mogelijk geen geschikte oplossing voor herstel na nood gevallen voor uw data bases. Omdat geo-replicatie gegevens asynchroon verzendt, kunnen recente updates in geval van nood verlies verloren gaan. Meer informatie over beperkingen van geo-replicatie wordt behandeld in de [geo-replicatie wordt niet ondersteund voor gegevens-en logboek bestanden op afzonderlijke schijven](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>HADR-implementatie architecturen
SQL Server HADR-technologieën die worden ondersteund in azure, zijn:

* [AlwaysOn-beschikbaarheids groepen](https://technet.microsoft.com/library/hh510230.aspx)
* [Altijd op instanties van het failovercluster](https://technet.microsoft.com/library/ms189134.aspx)
* [Logboek verzending](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server back-up en herstel met Azure Blob Storage service](https://msdn.microsoft.com/library/jj919148.aspx)
* [Data Base mirroring](https://technet.microsoft.com/library/ms189852.aspx) -afgeschaft in SQL Server 2016

Het is mogelijk om de technologieën samen te combi neren om een SQL Server oplossing te implementeren met hoge Beschik baarheid en mogelijkheden voor herstel na een nood geval. Afhankelijk van de technologie die u gebruikt, kan een hybride implementatie een VPN-tunnel vereisen met het virtuele Azure-netwerk. In de volgende secties ziet u enkele voor beelden van implementatie architecturen.

## <a name="azure-only-high-availability-solutions"></a>Alleen Azure: Oplossingen met hoge Beschik baarheid

U kunt een oplossing voor hoge Beschik baarheid hebben voor SQL Server op database niveau met AlwaysOn-beschikbaarheids groepen, die beschikbaarheids groepen worden genoemd. U kunt ook een oplossing met hoge Beschik baarheid maken op een instantie niveau met Always on failover cluster instances-failover cluster instances. Voor aanvullende redundantie kunt u redundantie op beide niveaus maken door beschikbaarheids groepen te maken op failover-cluster exemplaren. 

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheids groepen** |Beschikbaarheids replica's die worden uitgevoerd in virtuele Azure-machines in dezelfde regio bieden een hoge Beschik baarheid. U moet een VM van een domein controller configureren, omdat Windows Failover Clustering een Active Directory domein vereist.<br/> ![Beschikbaarheids groepen](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Zie [Availability groups configureren in azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)voor meer informatie. |
| **Failover-cluster exemplaren** |Failover cluster-instanties (FCI), waarvoor gedeelde opslag nodig is, kunnen op drie verschillende manieren worden gemaakt.<br/><br/>1. Een failovercluster met twee knoop punten dat wordt uitgevoerd in azure vm's met gekoppelde opslag met behulp van [Windows Server 2016 \(opslagruimten direct S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) om een virtueel San op basis van software te leveren.<br/><br/>2. Een failovercluster met twee knoop punten dat wordt uitgevoerd in azure Vm's met opslag die wordt ondersteund door een clusterings oplossing van derden. Voor een specifiek voor beeld dat gebruikmaakt van SIOS data keeper, raadpleegt [u hoge Beschik baarheid voor een bestands share met behulp van failover clustering en software van derden SIOS data keeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Een failovercluster met twee knoop punten dat wordt uitgevoerd in azure Vm's met externe iSCSI-doel gedeelde blok opslag via ExpressRoute. NetApp Private Storage (NPS) biedt bijvoorbeeld een iSCSI-doel via ExpressRoute met Equinix voor Azure-Vm's.<br/><br/>Voor gedeelde opslag-en gegevens replicatie oplossingen van derden neemt u contact op met de leverancier voor problemen die betrekking hebben op het openen van gegevens in de failover.<br/><br/>Houd er rekening mee dat het gebruik van FCI boven op [Azure File Storage](https://azure.microsoft.com/services/storage/files/) nog niet wordt ondersteund, omdat deze oplossing geen gebruik maakt van Premium Storage. We werken binnenkort aan de slag. |

## <a name="azure-only-disaster-recovery-solutions"></a>Alleen Azure: Oplossingen voor herstel na noodgevallen
U kunt een oplossing voor herstel na nood geval hebben voor uw SQL Server-data bases in azure met behulp van beschikbaarheids groepen, database spiegeling of back-up en herstel met opslag-blobs.

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheids groepen** |Beschikbaarheids replica's die worden uitgevoerd op meerdere data centers in azure-Vm's voor nood herstel. Deze oplossing voor meerdere regio's biedt beveiliging tegen volledige onderbrekingen van de site. <br/> ![Beschikbaarheids groepen](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Binnen een regio moeten alle replica's zich binnen dezelfde Cloud service en hetzelfde VNet bevinden. Omdat elke regio een afzonderlijk VNet heeft, is voor deze oplossingen VNet vereist voor de VNet-connectiviteit. Zie [een vnet-naar-VNet-verbinding configureren met behulp van de Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)voor meer informatie. Zie voor gedetailleerde instructies [een SQL Server-beschikbaarheids groep configureren in Azure virtual machines in verschillende regio's](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Database spiegeling** |Principal en mirror en servers die worden uitgevoerd in verschillende data centers voor herstel na nood gevallen. U moet implementeren met server certificaten. SQL Server Data Base mirroring wordt niet ondersteund voor SQL Server 2008 en SQL Server 2008 R2 op een virtuele machine van Azure. <br/>![Database spiegeling](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Back-ups maken en herstellen met Azure Blob Storage-service** |Productie databases maken rechtstreeks back-ups van Blob Storage in een ander Data Center voor herstel na nood gevallen.<br/>![Back-up en herstel](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Zie [back-up en herstel voor SQL Server in Azure virtual machines](virtual-machines-windows-sql-backup-recovery.md)voor meer informatie. |
| **Repliceren en failover-SQL Server naar Azure met Azure Site Recovery** |De productie SQL Server van een Azure-Data Center dat rechtstreeks wordt gerepliceerd naar Azure Storage van een ander Azure-Data Center voor herstel na nood gevallen.<br/>![Repliceren met Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server nood herstel en Azure site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybride IT: Oplossingen voor herstel na noodgevallen
U kunt een oplossing voor herstel na nood geval hebben voor uw SQL Server-data bases in een hybride omgeving met beschik baarheids groepen, database spiegeling, logboek verzending en back-up en herstel met Azure-blog opslag.

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheids groepen** |Sommige beschikbaarheids replica's die worden uitgevoerd in azure-Vm's en andere replica's die on-premises actief zijn voor herstel na nood geval op meerdere locaties. De productie site kan on-premises of in een Azure-Data Center zijn.<br/>![Beschikbaarheids groepen](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Omdat alle beschikbaarheids replica's zich in hetzelfde failovercluster moeten bevinden, moet het cluster beide netwerken (een failover-cluster met meerdere subnetten) beslaan. Voor deze configuratie is een VPN-verbinding tussen Azure en het on-premises netwerk vereist.<br/><br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel.<br/><br/>U kunt de wizard replica toevoegen in SSMS gebruiken om een Azure-replica toe te voegen aan een bestaande AlwaysOn-beschikbaarheids groep. Zie voor meer informatie zelf studie: Breid uw AlwaysOn-beschikbaarheids groep uit naar Azure. |
| **Database spiegeling** |Een partner die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd voor nood herstel op meerdere sites met server certificaten. Partners hoeven zich niet in hetzelfde Active Directory domein te bevinden en er is geen VPN-verbinding vereist.<br/>![Database spiegeling](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Een ander scenario voor het spie gelen van data bases bestaat uit één partner die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd in hetzelfde Active Directory domein voor nood herstel op meerdere locaties. Er is een [VPN-verbinding tussen het virtuele netwerk van Azure en het on-premises netwerk](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) vereist.<br/><br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel. SQL Server Data Base mirroring wordt niet ondersteund voor SQL Server 2008 en SQL Server 2008 R2 op een virtuele machine van Azure. |
| **Logboek verzending** |Een server die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd voor nood herstel op meerdere locaties. Logboek verzending is afhankelijk van het delen van Windows-bestanden, dus een VPN-verbinding tussen het virtuele netwerk van Azure en het on-premises netwerk is vereist.<br/>![Logboek verzending](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel. |
| **Back-ups maken en herstellen met Azure Blob Storage-service** |On-premises productie databases maken rechtstreeks back-ups van Azure Blob-opslag voor herstel na nood gevallen.<br/>![Back-up en herstel](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Zie [back-up en herstel voor SQL Server in Azure virtual machines](virtual-machines-windows-sql-backup-recovery.md)voor meer informatie. |
| **Repliceren en failover-SQL Server naar Azure met Azure Site Recovery** |On-premises productie SQL Server rechtstreeks gerepliceerd naar Azure Storage voor herstel na nood gevallen.<br/>![Repliceren met Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server nood herstel en Azure site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Belang rijke aandachtspunten voor SQL Server HADR in azure
Azure-Vm's,-opslag en-netwerken hebben verschillende operationele kenmerken dan een on-premises, niet-gevirtualiseerde IT-infra structuur. Een geslaagde implementatie van een HADR SQL Server oplossing in azure vereist dat u deze verschillen begrijpt en uw oplossing ontwerpt om deze te kunnen ondersteunen.

### <a name="high-availability-nodes-in-an-availability-set"></a>Knoop punten met hoge Beschik baarheid in een beschikbaarheidsset
Met beschikbaarheids sets in azure kunt u de Maxi maal beschik bare knoop punten in afzonderlijke fout domeinen (Fd's) en update domeinen (UDs) plaatsen. Aan elke virtuele machine in uw beschikbaarheidsset wordt een update domein en een fout domein toegewezen door het onderliggende Azure-platform. Deze configuratie binnen een Data Center zorgt ervoor dat tijdens een geplande of niet-geplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de 99,95% Azure SLA. Als u de instelling voor hoge Beschik baarheid wilt configureren, plaatst u alle deelnemende SQL-Virtual Machines in dezelfde beschikbaarheidsset om te voor komen dat toepassingen of gegevens verloren gaan tijdens een onderhouds gebeurtenis. Alleen knoop punten in dezelfde Cloud service kunnen deel nemen aan dezelfde beschikbaarheidsset. Zie voor meer informatie [De beschikbaarheid van virtuele machines beheren](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Knoop punten met hoge Beschik baarheid in een beschikbaarheids zone
Beschikbaarheidszones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. De fysieke schei ding van Beschikbaarheidszones binnen een regio beveiligt toepassingen en gegevens van datacenter fouten door ervoor te zorgen dat er ten minste één virtuele machine beschikbaar is en voldoet aan 99,99% Azure SLA. Als u maximale Beschik baarheid wilt configureren, plaatst u deelnemende SQL Virtual Machines verspreid over de beschik bare Beschikbaarheidszones in de regio. Er zijn extra kosten voor de gegevens overdracht van VM-naar-VM-zone met een andere Beschik baarheid. Zie [beschikbaarheids zones](/azure/availability-zones/az-overview)voor meer informatie. 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Gedrag van failover-clusters in azure-netwerken
De niet-RFC-compatibele DHCP-service in azure kan ertoe leiden dat het maken van bepaalde failover-cluster configuraties mislukt, omdat er een dubbel IP-adres aan de cluster naam wordt toegewezen, zoals hetzelfde IP-adres als een van de cluster knooppunten. Dit is een probleem bij het implementeren van beschikbaarheids groepen, die afhankelijk zijn van de functie Windows-failovercluster.

Denk na over het scenario wanneer een cluster met twee knoop punten wordt gemaakt en online wordt gebracht:

1. Het cluster is online en vervolgens KNOOPPUNT1 vraagt een dynamisch toegewezen IP-adres aan voor de naam van het cluster netwerk.
2. De DHCP-service krijgt geen ander IP-adres dan het eigen IP-adres van NODE1's, omdat de DHCP-service detecteert dat de aanvraag afkomstig is van KNOOPPUNT1 zelf.
3. Windows detecteert dat er een dubbel adres is toegewezen aan KNOOPPUNT1 en aan de netwerk naam van het failovercluster en dat de standaard cluster groep niet online kan worden gebracht.
4. De standaard cluster groep wordt verplaatst naar KNOOPPUNT2, die NODE1's IP-adres beschouwt als het IP-adres van het cluster en de standaard cluster groep online brengt.
5. Wanneer KNOOPPUNT2 probeert verbinding te maken met KNOOPPUNT1, hebben pakketten die zijn gericht op KNOOPPUNT1 nooit KNOOPPUNT2, omdat het IP-adres van NODE1's wordt omgezet naar zichzelf. KNOOPPUNT2 kan geen verbinding maken met KNOOPPUNT1, het quorum wordt verloren gegaan en het cluster wordt afgesloten.
6. Ondertussen kan KNOOPPUNT1 pakketten verzenden naar KNOOPPUNT2, maar KNOOPPUNT2 kan niet antwoorden. KNOOPPUNT1 verliest quorum en sluit het cluster af.

Dit scenario kan worden vermeden door een niet-gebruikt statisch IP-adres toe te wijzen, zoals een link-local IP-adres, zoals 169.254.1.1, aan de naam van het cluster netwerk om de naam van het cluster netwerk online te brengen. Zie [Windows-failovercluster configureren in azure voor beschikbaarheids groepen](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)om dit proces te vereenvoudigen.

Zie [Availability groups configureren in azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)voor meer informatie.

### <a name="availability-group-listener-support"></a>Ondersteuning voor beschikbaarheids groep-listener
Listeners voor beschikbaarheids groep worden ondersteund op virtuele Azure-machines met Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 en Windows Server 2016. Deze ondersteuning wordt mogelijk gemaakt door het gebruik van eind punten met taak verdeling die zijn ingeschakeld op de virtuele Azure-machines die de knoop punten van de beschikbaarheids groep zijn. U moet speciale configuratie stappen uitvoeren om te zorgen dat de listeners werken voor zowel client toepassingen die in Azure worden uitgevoerd als de taken die on-premises worden uitgevoerd.

Er zijn twee belang rijke opties voor het instellen van uw listener: extern (openbaar) of intern. De externe (open bare) listener maakt gebruik van een Internet gerichte load balancer en is gekoppeld aan een openbaar virtueel IP-adres (VIP) dat toegankelijk is via internet. Een interne listener maakt gebruik van een interne load balancer en ondersteunt alleen clients binnen dezelfde Virtual Network. Voor beide load balancer type moet u direct server return inschakelen. 

Als de beschikbaarheids groep meerdere Azure-subnetten omvat (zoals een implementatie die Azure-regio's kruist), moet de Client connection string '**MultisubnetFailover = True**' bevatten. Dit resulteert in een parallelle verbinding met de replica's in de verschillende subnetten. Zie voor instructies voor het instellen van een listener.

* [Een ILB-listener configureren voor beschikbaarheids groepen in azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Een externe listener configureren voor beschikbaarheids groepen in azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

U kunt nog steeds afzonderlijk verbinding maken met elke beschikbaarheids replica door rechtstreeks verbinding te maken met het service-exemplaar. Omdat beschikbaarheids groepen achterwaarts compatibel zijn met data base mirroring-clients, kunt u ook verbinding maken met de beschikbaarheids replica's, zoals database spiegel partners, zolang de replica's zijn geconfigureerd als het spie gelen van data bases:

* Eén primaire replica en één secundaire replica
* De secundaire replica is geconfigureerd als niet-lees bare,**secundaire** optie ingesteld op **Nee**.

Een voor beeld-Client connection string die overeenkomt met deze gespiegelde, zoals configuratie met behulp van ADO.NET of SQL Server Native Client onder meer:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Zie voor meer informatie over client connectiviteit:

* [Tref woorden voor de verbindings reeks gebruiken met SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Clients verbinden met een sessie voor het spie gelen van data bases (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Verbinding maken met de beschikbaarheids groep-listener in hybride IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Listeners voor beschikbaarheids groep, client connectiviteit en failover van toepassing (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Verbindings reeksen voor database spiegeling gebruiken met beschikbaarheids groepen](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Netwerk latentie in hybride IT
U moet uw HADR-oplossing implementeren met de veronderstelling dat er Peri Oden kunnen zijn met een hoge netwerk latentie tussen uw on-premises netwerk en Azure. Bij het implementeren van replica's in azure, moet u asynchrone door Voer gebruiken in plaats van synchrone door Voer voor de synchronisatie modus. Gebruik de modus voor hoge prestaties bij het implementeren van servers voor het spie gelen van data bases zowel on-premises als in azure, in plaats van de modus met hoge beveiliging.

### <a name="geo-replication-support"></a>Ondersteuning van geo-replicatie
Geo-replicatie in azure disks biedt geen ondersteuning voor het gegevens bestand en logboek bestand van dezelfde data base die op afzonderlijke schijven moet worden opgeslagen. GRS repliceert wijzigingen op elke schijf onafhankelijk en asynchroon. Dit mechanisme garandeert de schrijf volgorde binnen één schijf op de geo-gerepliceerde kopie, maar niet via Geo-gerepliceerde kopieën van meerdere schijven. Als u een Data Base configureert om het gegevens bestand en het logboek bestand op afzonderlijke schijven op te slaan, kan het zijn dat de herstelde schijven na een nood herstel een meer recente kopie van het gegevens bestand bevatten dan het logboek bestand, waardoor het voorschrijvende logboek in SQL Server wordt verbroken en de ACID-eigenschappen van Transa ctions. Als u niet de mogelijkheid hebt om geo-replicatie uit te scha kelen op het opslag account, moet u alle gegevens en logboek bestanden voor een bepaalde data base op dezelfde schijf bewaren. Als u meer dan één schijf moet gebruiken vanwege de grootte van de data base, moet u een van de hierboven genoemde nood herstel oplossingen implementeren om gegevens redundantie te garanderen.

## <a name="next-steps"></a>Volgende stappen
Als u een virtuele machine van Azure met SQL Server wilt maken, raadpleegt u [een SQL Server virtuele machine inrichten in azure](virtual-machines-windows-portal-sql-server-provision.md).

Zie de richt lijnen in [Best practices voor SQL Server in azure virtual machines voor](virtual-machines-windows-sql-performance.md)de beste prestaties van SQL Server die worden uitgevoerd op een virtuele Azure-machine.

Zie [SQL Server op azure virtual machines](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's.

### <a name="other-resources"></a>Meer informatie
* [Een nieuwe Active Directory-forest in azure installeren](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Failovercluster voor beschikbaarheids groepen maken in azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

