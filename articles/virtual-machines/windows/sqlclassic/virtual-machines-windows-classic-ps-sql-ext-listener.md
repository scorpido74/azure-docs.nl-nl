---
title: Een externe listener configureren voor beschikbaarheids groepen
description: In deze zelf studie wordt u begeleid bij het maken van een always on-beschikbaarheids groep-listener in azure die extern toegankelijk is met behulp van het open bare virtuele IP-adres van de bijbehorende Cloud service.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75978181"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Een externe listener configureren voor beschikbaarheids groepen op Azure SQL Server Vm's
> [!div class="op_single_selector"]
> * [Interne listener](../classic/ps-sql-int-listener.md)
> * [Externe listener](../classic/ps-sql-ext-listener.md)
> 
> 

In dit onderwerp wordt beschreven hoe u een listener kunt configureren voor een AlwaysOn-beschikbaarheids groep die extern toegankelijk is op internet. Dit wordt mogelijk gemaakt door het **open bare VIP-adres (virtuele IP)** van de Cloud service te koppelen aan de listener.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Uw beschikbaarheids groep kan replica's bevatten die alleen on-premises zijn, alleen Azure, of die zowel on-premises als Azure omvatten voor hybride configuraties. Azure-replica's kunnen zich in dezelfde regio of in meerdere regio's bevinden met meerdere virtuele netwerken (VNets). In de onderstaande stappen wordt ervan uitgegaan dat u al [een beschikbaarheids groep hebt geconfigureerd](../classic/portal-sql-alwayson-availability-groups.md) , maar geen listener hebt geconfigureerd.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Richt lijnen en beperkingen voor externe listeners
Let op de volgende richt lijnen over de beschikbaarheids groep-listener in azure wanneer u implementeert met behulp van het open bare VIP-adres van de Cloud service:

* De beschikbaarheids groep-listener wordt ondersteund op Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2.
* De client toepassing moet zich bevinden op een andere Cloud service dan die van de virtuele machines met de beschikbaarheids groep. Azure biedt geen ondersteuning voor Direct Server Return met client en server in dezelfde Cloud service.
* In de stappen in dit artikel ziet u standaard hoe u één listener zo configureert dat het VIP-adres (virtuele IP) van de Cloud service wordt gebruikt. Het is echter mogelijk om meerdere VIP-adressen te reserveren en te maken voor uw Cloud service. Op deze manier kunt u de stappen in dit artikel gebruiken om meerdere listeners te maken die aan een ander VIP zijn gekoppeld. Zie [meerdere vip's per Cloud service](../../../load-balancer/load-balancer-multivip.md)voor meer informatie over het maken van meerdere VIP-adressen.
* Als u een listener voor een hybride omgeving maakt, moet het on-premises netwerk verbinding hebben met het open bare Internet naast het site-naar-site-VPN met het virtuele Azure-netwerk. In het Azure-subnet is de beschikbaarheids groep-listener alleen bereikbaar voor het open bare IP-adres van de betreffende Cloud service.
* Het is niet mogelijk om een externe listener te maken in dezelfde Cloud service als u ook een interne listener hebt die gebruikmaakt van de interne Load Balancer (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>De toegankelijkheid van de listener bepalen
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

In dit artikel wordt aandacht besteed aan het maken van een listener die gebruikmaakt van **externe taak verdeling**. Als u een listener wilt die privé is voor uw virtuele netwerk, raadpleegt u de versie van dit artikel die de stappen biedt voor het instellen van een [listener met ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>VM-eind punten met taak verdeling maken met Direct Server Return
Externe taak verdeling maakt gebruik van het virtuele open bare virtuele IP-adres van de Cloud service die als host fungeert voor uw Vm's. U hoeft de load balancer dus niet in dit geval te maken of configureren.

U moet een eind punt met gelijke taak verdeling maken voor elke VM die als host fungeert voor een Azure replica. Als u replica's in meerdere regio's hebt, moet elke replica voor die regio zich in dezelfde Cloud service bevinden in hetzelfde VNet. Voor het maken van replica's van beschikbaarheids groepen die meerdere Azure-regio's omvatten, moet meerdere VNets worden geconfigureerd. Zie [Vnet configureren voor vnet-connectiviteit](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)voor meer informatie over het configureren van multi VNet-connectiviteit.

1. In de Azure Portal gaat u naar elke VM die als host fungeert voor een replica en bekijkt u de details.
2. Klik op het tabblad **eind punten** voor elke virtuele machine.
3. Controleer of de **naam** en de **open bare poort** van het listener-eind punt dat u wilt gebruiken niet al in gebruik is. In het onderstaande voor beeld is de naam ' MyEndpoint ' en is de poort ' 1433 '.
4. Down load en Installeer [de meest recente Power shell-module](https://azure.microsoft.com/downloads/)op de lokale client.
5. Start **Azure PowerShell**. Er wordt een nieuwe Power shell-sessie geopend met de Azure-beheer modules geladen.
6. Voer **Get-AzurePublishSettingsFile**uit. Met deze cmdlet wordt u omgeleid naar een browser voor het downloaden van een bestand met publicatie-instellingen naar een lokale map. U wordt mogelijk gevraagd om uw aanmeldings referenties voor uw Azure-abonnement.
7. Voer de opdracht **import-AzurePublishSettingsFile** uit met het pad van het publicatie-instellingen bestand dat u hebt gedownload:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Zodra het publicatie-instellingen bestand is geïmporteerd, kunt u uw Azure-abonnement beheren in de Power shell-sessie.
    
1. Kopieer het onderstaande Power shell-script naar een tekst editor en stel de waarden van de variabele in op uw omgeving (de standaard waarden zijn opgegeven voor sommige para meters). Als uw beschikbaarheids groep Azure-regio's omvat, moet u het script eenmaal uitvoeren in elk Data Center voor de Cloud service en knoop punten die zich in dat Data Center bevinden.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Nadat u de variabelen hebt ingesteld, kopieert u het script van de tekst editor naar uw Azure PowerShell-sessie om het uit te voeren. Als de prompt nog >> wordt weer gegeven, typt u nogmaals ENTER om te controleren of het script wordt uitgevoerd.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Controleer of KB2854082 is geïnstalleerd, indien nodig
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Open de firewall poorten in beschikbaarheids groep knoop punten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>De listener voor beschikbaarheidsgroep maken

Maak de beschikbaarheids groep-listener in twee stappen. Maak eerst de cluster bron voor het client toegangs punt en configureer afhankelijkheden. Ten tweede configureert u de cluster bronnen met Power shell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Het client toegangs punt maken en de cluster afhankelijkheden configureren
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>De cluster resources configureren in Power shell
1. Voor externe taak verdeling moet u het open bare virtuele IP-adres van de Cloud service met uw replica's ophalen. Meld u aan bij de Azure-portal. Ga naar de Cloud service die de virtuele machine met de beschikbaarheids groep bevat. Open de **Dashboard** weergave.
2. Noteer het adres dat wordt weer gegeven onder het **open bare VIP-adres (virtuele IP)**. Als uw oplossing VNets omvat, herhaalt u deze stap voor elke Cloud service die een virtuele machine bevat die als host fungeert voor een replica.
3. Kopieer op een van de virtuele machines het Power shell-script hieronder naar een tekst editor en stel de variabelen in op de waarden die u eerder hebt genoteerd.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Nadat u de variabelen hebt ingesteld, opent u een Windows Power shell-venster met verhoogde bevoegdheden en kopieert u het script uit de tekst editor en plakt u het in uw Azure PowerShell-sessie om het uit te voeren. Als de prompt nog >> wordt weer gegeven, typt u nogmaals ENTER om te controleren of het script wordt uitgevoerd.
5. Herhaal dit op elke virtuele machine. Met dit script wordt de IP-adres bron geconfigureerd met het IP-adres van de Cloud service en worden andere para meters ingesteld, zoals de test poort. Wanneer de IP-adres bron online wordt gebracht, kan deze reageren op de polling op de test poort van het eind punt met taak verdeling dat eerder in deze zelf studie is gemaakt.

## <a name="bring-the-listener-online"></a>De listener online brengen
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Opvolgings items
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>De listener voor de beschikbaarheids groep testen (binnen hetzelfde VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>De listener voor de beschikbaarheids groep testen (via internet)
Als u toegang wilt krijgen tot de listener van buiten het virtuele netwerk, moet u externe/open bare taak verdeling gebruiken (zoals beschreven in dit onderwerp) in plaats van ILB, die alleen toegankelijk is in hetzelfde VNet. Geef in het connection string de naam van de Cloud service op. Als u bijvoorbeeld een Cloud service met de naam *mycloudservice*had, zou de Sqlcmd-instructie er als volgt uitzien:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

In tegens telling tot het vorige voor beeld moet SQL-verificatie worden gebruikt, omdat de aanroeper geen Windows-verificatie via internet kan gebruiken. Zie AlwaysOn- [beschikbaarheids groep in azure VM: client connectiviteits scenario's](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)voor meer informatie. Wanneer u SQL-verificatie gebruikt, moet u dezelfde aanmelding op beide replica's maken. Zie [aanmeldingen toewijzen of opgenomen SQL database gebruiker om verbinding te maken met andere replica's en te koppelen aan beschikbaarheids databases](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)voor meer informatie over het oplossen van problemen met aanmeldingen met beschikbaarheids groepen.

Als de always on-replica's zich in verschillende subnetten bevinden, moeten clients **MultisubnetFailover = True** opgeven in de Connection String. Dit resulteert in een parallelle verbinding en probeert replica's in de verschillende subnetten. Houd er rekening mee dat dit scenario een permanente implementatie van een beschikbaarheids groep voor meerdere regio's omvat.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

