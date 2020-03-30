---
title: Beschikbaarheidsgroep configureren in verschillende regio's
description: In dit artikel wordt uitgelegd hoe u een SQL Server-beschikbaarheidsgroep configureert op virtuele Azure-machines met een replica in een andere regio.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060114"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Een beschikbaarheidsgroep configureren op virtuele Azure Server-machines in verschillende regio's

In dit artikel wordt uitgelegd hoe u een SQL Server Always On availability group replica configureert op Virtuele Azure-machines op een externe Azure-locatie. Gebruik deze configuratie om noodherstel te ondersteunen.

Dit artikel is van toepassing op Azure Virtual Machines in de Resource Manager-modus.

In de volgende afbeelding wordt een algemene implementatie van een beschikbaarheidsgroep op virtuele Azure-machines weergegeven:

   ![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

In deze implementatie bevinden alle virtuele machines zich in één Azure-regio. De replica's van de beschikbaarheidsgroep kunnen synchrone commit hebben met automatische failover op SQL-1 en SQL-2. Zie [Sjabloon beschikbaarheidsgroep of zelfstudie](virtual-machines-windows-portal-sql-availability-group-overview.md)voor het bouwen van deze architectuur.

Deze architectuur is kwetsbaar voor downtime als de Azure-regio ontoegankelijk wordt. Als u dit beveiligingslek wilt verhelpen, voegt u een replica toe in een andere Azure-regio. In het volgende diagram ziet u hoe de nieuwe architectuur eruit zou zien:

   ![Beschikbaarheidsgroep DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Het voorgaande diagram toont een nieuwe virtuele machine genaamd SQL-3. SQL-3 bevindt zich in een andere Azure-regio. SQL-3 wordt toegevoegd aan het Failovercluster van Windows Server. SQL-3 kan een replica van de beschikbaarheidsgroep hosten. Tot slot moet u zien dat de Azure-regio voor SQL-3 een nieuwe Azure-load balancer heeft.

>[!NOTE]
> Er is een Azure-beschikbaarheidsset vereist wanneer meer dan één virtuele machine zich in dezelfde regio bevindt. Als er slechts één virtuele machine in de regio is, is de beschikbaarheidsset niet vereist. U een virtuele machine alleen in een beschikbaarheidsset plaatsen op het moment van creatie. Als de virtuele machine zich al in een beschikbaarheidsset bevindt, u later een virtuele machine toevoegen voor een extra replica.

In deze architectuur wordt de replica in het externe gebied normaal geconfigureerd met de beschikbaarheidsmodus voor asynchrone commit en handmatige failovermodus.

Wanneer replica's van beschikbaarheidsgroepen zich op virtuele Azure-machines in verschillende Azure-regio's bevinden, vereist elke regio:

* Een virtuele netwerkgateway
* Een verbinding met virtuele netwerkgateway

In het volgende diagram ziet u hoe de netwerken communiceren tussen datacenters.

   ![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Deze architectuur brengt uitgaande gegevens kosten met zich mee voor gegevens die worden gerepliceerd tussen Azure-regio's. Zie [Bandbreedteprijzen](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Externe replica maken

Ga als volgt te werk om een replica te maken in een extern datacenter:

1. [Maak een virtueel netwerk in de nieuwe regio.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [Een VNet-naar-VNet-verbinding configureren met behulp van de Azure-portal.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

   >[!NOTE]
   >In sommige gevallen moet u PowerShell gebruiken om de VNet-naar-VNet-verbinding te maken. Als u bijvoorbeeld verschillende Azure-accounts gebruikt, u de verbinding in de portal niet configureren. In dit geval [zie, Een VNet-verbinding configureren met behulp van de Azure-portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Maak een domeincontroller in de nieuwe regio.](../../../active-directory/active-directory-new-forest-virtual-machine.md)

   Deze domeincontroller biedt verificatie als de domeincontroller op de primaire site niet beschikbaar is.

1. [Maak een virtuele SQL Server-machine in de nieuwe regio.](virtual-machines-windows-portal-sql-server-provision.md)

1. [Maak een Azure load balancer in het netwerk op de nieuwe regio.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

   Deze load balancer moet:

   - Be in hetzelfde netwerk en subnet als de nieuwe virtuele machine.
   - Een statisch IP-adres hebben voor de listener van de beschikbaarheidsgroep.
   - Voeg een backendpool toe die alleen bestaat uit de virtuele machines in dezelfde regio als de load balancer.
   - Gebruik een TCP-poortsonde die specifiek is voor het IP-adres.
   - Een regel voor taakverdeling die specifiek is voor de SQL Server in dezelfde regio.  
   - Wees een standaard load balancer als de virtuele machines in de backend pool geen deel uitmaken van een enkele beschikbaarheidset of virtuele machineschaalset. Voor meer informatie bekijk [het Azure Load Balancer Standard-overzicht](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Functie Failoverclustering toevoegen aan de nieuwe SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Sluit u aan bij de nieuwe SQL Server in het domein.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

1. [Stel het nieuwe SQL Server-serviceaccount in om een domeinaccount te gebruiken.](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount)

1. [Voeg de nieuwe SQL Server toe aan het Failovercluster van Windows Server.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode)

1. Voeg een IP-adresbron toe aan het cluster.

   U de IP-adresbron maken in Failoverclusterbeheer. Selecteer de naam van het cluster en klik met de rechtermuisknop op de clusternaam onder **Clusterkernbronnen** en selecteer **Eigenschappen:** 

   ![Clustereigenschappen](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   Selecteer **in** het dialoogvenster **Eigenschappen** De optie Toevoegen onder **IP-adres**en voeg vervolgens het IP-adres van de clusternaam toe vanuit het externe netwerkgebied. Selecteer **OK** in het dialoogvenster **IP-adres** en selecteer **ok** opnieuw in het dialoogvenster **Clustereigenschappen** om het nieuwe IP-adres op te slaan.. 

   ![Cluster-IP toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Voeg het IP-adres toe als afhankelijkheid voor de naam van het kerncluster.

   Open de clustereigenschappen nogmaals en selecteer het tabblad **Afhankelijkheden.** Een OK-afhankelijkheid configureren voor de twee IP-adressen: 

   ![Clustereigenschappen](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Voeg een IP-adresbron toe aan de rol van beschikbaarheidsgroep in het cluster. 

   Klik met de rechtermuisknop op de rol van de beschikbaarheidsgroep in Failoverclusterbeheer, selecteer **Resource**toevoegen , **Meer resources**en selecteer **IP-adres**.

   ![IP-adres maken](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configureer dit IP-adres als volgt:

   - Gebruik het netwerk vanuit het externe datacenter.
   - Wijs het IP-adres toe aan de nieuwe Azure load balancer. 

1. Voeg de IP-adresbron toe als afhankelijkheid voor het cluster listenerclientaccesspoint (netwerknaam).

   In de volgende schermafbeelding wordt een goed geconfigureerde IP-adresclusterbron weergegeven:

   ![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >De clusterbrongroep bevat beide IP-adressen. Beide IP-adressen zijn afhankelijkheden voor het toegangspunt voor listenerclient. Gebruik de **OPERATOR OR** in de configuratie van clusterafhankelijkheid.

1. [Stel de clusterparameters in PowerShell in](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Voer het PowerShell-script uit met de naam van het clusternetwerk, het IP-adres en de sondepoort die u hebt geconfigureerd op de load balancer in het nieuwe gebied.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Schakel [always on availability-groepen in](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)op de nieuwe SQL Server in SQL Server Configuration Manager .

1. [Open firewallpoorten op de nieuwe SQL Server.](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)

   De poortnummers die u moet openen, zijn afhankelijk van uw omgeving. Open poorten voor het mirroring-eindpunt en azure load balancer health probe.


1. [Voeg een replica toe aan de beschikbaarheidsgroep op de nieuwe SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Voor een replica in een extern Azure-gebied stelt u deze in voor asynchrone replicatie met handmatige failover.  

## <a name="set-connection-for-multiple-subnets"></a>Verbinding instellen voor meerdere subnetten

De replica in het externe datacenter maakt deel uit van de beschikbaarheidsgroep, maar staat in een ander subnet. Als deze replica de primaire replica wordt, kunnen time-outs van toepassingsverbindingen optreden. Dit gedrag is hetzelfde als een on-premises beschikbaarheidsgroep in een multi-subnetimplementatie. Als u verbindingen van clienttoepassingen wilt toestaan, werkt u de clientverbinding bij of configureert u de cache van de naamresolutie op de clusternetwerknaambron.

Bij voorkeur de clientverbindingstekenreeksen `MultiSubnetFailover=Yes`bijwerken om in te stellen . Zie [Verbinding maken met MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Als u de verbindingstekenreeksen niet wijzigen, u caching met naamresolutie configureren. Zie [Time-out-fout en u geen verbinding maken met een SQL Server 2012 AlwaysOn-typelistener voor beschikbaarheid in een multi-subnetomgeving.](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av)

## <a name="fail-over-to-remote-region"></a>Overgaan naar het afgelegen gebied

Als u de verbinding met de listener met het externe gebied wilt testen, u de replica naar het externe gebied niet meer gebruiken. Hoewel de replica asynchroon is, is failover kwetsbaar voor mogelijk gegevensverlies. Als u wilt mislukken zonder gegevensverlies, wijzigt u de beschikbaarheidsmodus in synchroon en stelt u de failovermodus in op automatisch. Voer de volgende stappen uit:

1. Maak in **Object Explorer**verbinding met de instantie SQL Server waarmee de primaire replica wordt host.
1. Klik onder **Beschikbaarheidsgroepen voor AlwaysOn**, **Beschikbaarheidsgroepen**met de rechtermuisknop op uw beschikbaarheidsgroep en klik op **Eigenschappen**.
1. Stel op de pagina **Algemeen** onder **Beschikbaarheidsreplica's**de secundaire replica op de DR-site in op **de beschikbaarheidsmodus Synchrone commit** en de modus **Automatische** failover.
1. Als u een secundaire replica op dezelfde site hebt als uw primaire replica voor hoge beschikbaarheid, stelt u deze replica in op **Asynchrone commit** en **handleiding**.
1. Klik op OK.
1. Klik in **Object Explorer**met de rechtermuisknop op de groep beschikbaarheid en klik op **Dashboard weergeven**.
1. Controleer op het dashboard of de replica op de DR-site is gesynchroniseerd.
1. Klik in **Object Explorer**met de rechtermuisknop op de beschikbaarheidsgroep en klik op **Failover...**. SQL Server Management Studios opent een wizard om te mislukken via SQL Server.  
1. Klik **op Volgende**en selecteer de SQL Server-instantie op de DR-site. Klik nogmaals op **Volgende.**
1. Maak verbinding met de SQL Server-instantie op de DR-site en klik op **Volgende**.
1. Controleer **op** de pagina Overzicht de instellingen en klik op **Voltooien**.

Nadat u de connectiviteit hebt getest, verplaatst u de primaire replica terug naar uw primaire datacenter en stelt u de beschikbaarheidsmodus weer in op de normale bedrijfsinstellingen. In de volgende tabel worden de normale operationele instellingen voor de architectuur weergegeven die in dit document wordt beschreven:

| Locatie | Serverinstantie | Rol | Beschikbaarheidsmodus | Failovermodus
| ----- | ----- | ----- | ----- | -----
| Primair datacenter | SQL-1 | Primair | Synchrone | Automatisch
| Primair datacenter | SQL-2 | Secundair | Synchrone | Automatisch
| Secundair of extern datacenter | SQL-3 | Secundair | Asynchrone | Handmatig


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Meer informatie over geplande en gedwongen handmatige failover

Zie de volgende onderwerpen voor meer informatie:

- [Een geplande handmatige failover van een beschikbaarheidsgroep (SQL Server) uitvoeren](https://msdn.microsoft.com/library/hh231018.aspx)
- [Een geforceerde handmatige failover van een beschikbaarheidsgroep (SQL Server) uitvoeren](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Aanvullende koppelingen

* [Beschikbaarheidsgroepen altijd op beschikbaarheid](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure Load Balancers](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure-beschikbaarheidssets](../manage-availability.md)
