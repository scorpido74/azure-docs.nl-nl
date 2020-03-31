---
title: Een externe listener configureren voor beschikbaarheidsgroepen
description: In deze zelfstudie u stappen doorlopen bij het maken van een always on availability group listener in Azure die extern toegankelijk is met behulp van het openbare virtuele IP-adres van de bijbehorende cloudservice.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978181"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Een externe listener configureren voor beschikbaarheidsgroepen op Azure SQL Server VM's
> [!div class="op_single_selector"]
> * [Interne listener](../classic/ps-sql-int-listener.md)
> * [Externe listener](../classic/ps-sql-ext-listener.md)
> 
> 

In dit onderwerp ziet u hoe u een listener configureert voor een always on beschikbaarheidsgroep die extern toegankelijk is op internet. Dit wordt mogelijk gemaakt door het **openbare Virtual IP (VIP)** adres van de cloudservice te associëren met de luisteraar.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Uw beschikbaarheidsgroep kan replica's bevatten die alleen on-premises zijn, alleen Azure, of die zowel on-premises als Azure omvatten voor hybride configuraties. Azure-replica's kunnen zich binnen dezelfde regio of in meerdere regio's bevinden met meerdere virtuele netwerken (VNets). In de onderstaande stappen wordt ervan uitgegaan dat u [al een beschikbaarheidsgroep](../classic/portal-sql-alwayson-availability-groups.md) hebt geconfigureerd, maar geen listener hebt geconfigureerd.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Richtlijnen en beperkingen voor externe luisteraars
Let op de volgende richtlijnen voor de listener van de beschikbaarheidsgroep in Azure wanneer u het openbare VIP-adres van de cloudservice implementeert:

* De listener van de beschikbaarheidsgroep wordt ondersteund op Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2.
* De clienttoepassing moet zich op een andere cloudservice bevinden dan de service die uw beschikbaarheidsgroep VM's bevat. Azure ondersteunt geen directe serverretour met client en server in dezelfde cloudservice.
* Standaard laten de stappen in dit artikel zien hoe u één listener configureert om het VIP-adres (Virtual IP) (Cloud Service Te gebruiken). Het is echter mogelijk om meerdere VIP-adressen te reserveren en te maken voor uw cloudservice. Hiermee u de stappen in dit artikel gebruiken om meerdere listeners te maken die elk zijn gekoppeld aan een andere VIP. Zie [Meerdere VIP's per cloudservice](../../../load-balancer/load-balancer-multivip.md)voor informatie over het maken van meerdere VIP-adressen.
* Als u een listener maakt voor een hybride omgeving, moet het on-premises netwerk naast de site-to-site VPN met het virtuele Azure-netwerk ook verbinding hebben met het openbare internet. Wanneer in het Subnet Azure de listener van de beschikbaarheidsgroep alleen bereikbaar is via het openbare IP-adres van de desbetreffende cloudservice.
* Het wordt niet ondersteund om een externe listener te maken in dezelfde cloudservice waar u ook een interne listener hebt met behulp van de Internal Load Balancer (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>De toegankelijkheid van de luisteraar bepalen
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Dit artikel richt zich op het maken van een listener die externe **taakverdeling**gebruikt. Als u een listener wilt die privé is voor uw virtuele netwerk, raadpleegt u de versie van dit artikel met stappen voor het instellen van een [listener met ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Load-balanced VM-eindpunten maken met direct serverrendement
Externe taakverdeling maakt gebruik van het virtuele virtuele IP-adres van de cloudservice die uw VM's host. U hoeft in dit geval dus de load balancer niet te maken of te configureren.

U moet een eindpunt voor de uitrusting maken voor elke VM die een Azure-replica host. Als u replica's in meerdere regio's hebt, moet elke replica voor die regio zich in dezelfde cloudservice in hetzelfde VNet bevinden. Voor het maken van replica's van beschikbaarheidsgroepen die meerdere Azure-regio's omvatten, moeten meerdere VNets worden geconfigureerd. Zie VNet configureren voor [VNet-connectiviteit voor](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)meer informatie over het configureren van cross VNet-connectiviteit.

1. Navigeer in de Azure-portal naar elke VM die een replica host en bekijk de details.
2. Klik op het tabblad **Eindpunten** voor elk van de VM's.
3. Controleer of de **naam** en **de openbare poort** van het listenereindpunt dat u wilt gebruiken, nog niet in gebruik is. In het onderstaande voorbeeld is de naam "MyEndpoint" en de poort "1433".
4. Download en installeer op uw lokale client [de nieuwste PowerShell-module.](https://azure.microsoft.com/downloads/)
5. **Azure PowerShell starten**. Er wordt een nieuwe PowerShell-sessie geopend met de Azure-beheermodules geladen.
6. Run **Get-azurepublishSettingsFile**. Deze cmdlet leidt u naar een browser om een bestand met publicatie-instellingen te downloaden naar een lokale map. Mogelijk wordt u gevraagd om uw aanmeldingsreferenties voor uw Azure-abonnement.
7. Voer de opdracht **Import-AzurePublishSettingsFile** uit met het pad van het publicatie-instellingenbestand dat u hebt gedownload:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Zodra het publicatie-instellingenbestand is geïmporteerd, u uw Azure-abonnement beheren in de PowerShell-sessie.
    
1. Kopieer het PowerShell-script hieronder naar een teksteditor en stel de variabele waarden in op uw omgeving (standaardinstellingen zijn verstrekt voor sommige parameters). Houd er rekening mee dat als uw beschikbaarheidsgroep Azure-regio's omvat, u het script eenmaal in elk datacenter moet uitvoeren voor de cloudservice en knooppunten die zich in dat datacenter bevinden.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Zodra u de variabelen hebt ingesteld, kopieert u het script van de teksteditor naar uw Azure PowerShell-sessie om het uit te voeren. Als de prompt nog steeds >> wordt weergegeven, typt u enter opnieuw om ervoor te zorgen dat het script wordt uitgevoerd.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Controleer indien nodig of KB2854082 is geïnstalleerd
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>De firewallpoorten openen in knooppunten van beschikbaarheidsgroepen
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>De authenticiteit van de beschikbaarheidsgroep maken

Maak de listener van de beschikbaarheidsgroep in twee stappen. Maak eerst de clientaccess point clusterbron en configureer afhankelijkheden. Configureer ten tweede de clusterbronnen met PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Het toegangspunt van de client maken en de clusterafhankelijkheden configureren
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>De clusterbronnen in PowerShell configureren
1. Voor externe taakverdeling moet u het openbare virtuele IP-adres van de cloudservice dat uw replica's bevat, verkrijgen. Meld u aan bij de Azure-portal. Navigeer naar de cloudservice met vm voor beschikbaarheidsgroep VM. Open de **dashboardweergave.**
2. Let op het adres dat wordt weergegeven onder **VIP-adres (Public Virtual IP).** Als uw oplossing VNets overspant, herhaalt u deze stap voor elke cloudservice die een VM bevat die een replica host.
3. Kopieer het PowerShell-script hieronder in een teksteditor en stel de variabelen in op de waarden die u eerder hebt opgemerkt.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Zodra u de variabelen hebt ingesteld, opent u een verhoogd Windows PowerShell-venster, kopieert u het script van de teksteditor en plakt u deze in uw Azure PowerShell-sessie om het uit te voeren. Als de prompt nog steeds >> wordt weergegeven, typt u enter opnieuw om ervoor te zorgen dat het script wordt uitgevoerd.
5. Herhaal dit op elke vm. Dit script configureert de IP-adresbron met het IP-adres van de cloudservice en stelt andere parameters in, zoals de sondepoort. Wanneer de IP-adresbron online wordt gebracht, kan deze vervolgens reageren op de polling op de sondepoort vanaf het eerder in deze zelfstudie gemaakte eindpunt met de belastingsbalans.

## <a name="bring-the-listener-online"></a>Breng de luisteraar online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Vervolgitems
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>De listener voor beschikbaarheidsgroepen testen (binnen hetzelfde VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Test de listener van de beschikbaarheidsgroep (via internet)
Om toegang te krijgen tot de luisteraar van buiten het virtuele netwerk, moet u externe / openbare load balancing gebruiken (beschreven in dit onderwerp) in plaats van ILB, die alleen toegankelijk is binnen hetzelfde VNet. In de verbindingstekenreeks geeft u de naam van de cloudservice op. Als u bijvoorbeeld een cloudservice had met de naam *mycloudservice,* zou de sqlcmd-instructie als volgt zijn:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

In tegenstelling tot het vorige voorbeeld moet SQL-verificatie worden gebruikt, omdat de beller geen Windows-verificatie via internet kan gebruiken. Zie [Always On Availability Group in Azure VM: Client Connectivity Scenarios](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)voor meer informatie. Controleer bij het gebruik van SQL-verificatie of u op beide replica's dezelfde aanmelding maakt. Zie [Aanmeldingen toewijzen of gebruik de inloggegevens van de SQL-database om verbinding te maken met andere replica's en kaart met beschikbaarheidsdatabases](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)voor meer informatie over het oplossen van aanmeldingen met beschikbaarheidsgroepen.

Als de replica's always on zich in verschillende subnetten bevinden, moeten clients **MultisubnetFailover=True** opgeven in de verbindingstekenreeks. Dit resulteert in parallelle verbindingspogingen tot replica's in de verschillende subnetten. Houd er rekening mee dat dit scenario een implementatie van always on availability group met verschillende regio's bevat.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

