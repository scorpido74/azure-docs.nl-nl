---
title: Een ILB-listener configureren voor beschikbaarheids groepen (klassiek)
description: In deze zelf studie wordt gebruikgemaakt van resources die zijn gemaakt met het klassieke implementatie model en wordt er een AlwaysOn-beschikbaarheids groep-listener gemaakt voor een SQL Server-VM in azure die gebruikmaakt van een interne load balancer.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f05e1d46485b337acbd9390441359e086067db74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014809"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Een ILB-listener configureren voor beschikbaarheids groepen op Azure SQL Server Vm's
> [!div class="op_single_selector"]
> * [Interne listener](../classic/ps-sql-int-listener.md)
> * [Externe listener](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Azure Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt het gebruik van het klassieke implementatie model besproken. Het is raadzaam om de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

Als u een listener wilt configureren voor een AlwaysOn-beschikbaarheids groep in het Resource Manager-model, raadpleegt u [een Load Balancer configureren voor een always on-beschikbaarheids groep in azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

Uw beschikbaarheids groep kan replica's bevatten die alleen on-premises zijn of alleen voor Azure, of die zowel on-premises als Azure omvatten voor hybride configuraties. Azure-replica's kunnen zich in dezelfde regio bevinden of in meerdere regio's die gebruikmaken van meerdere virtuele netwerken. In de procedures in dit artikel wordt ervan uitgegaan dat u al [een beschikbaarheids groep hebt geconfigureerd](../classic/portal-sql-alwayson-availability-groups.md) , maar nog geen listener hebt geconfigureerd.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Richt lijnen en beperkingen voor interne listeners
Het gebruik van een interne load balancer (ILB) met een listener van een beschikbaarheids groep in Azure is onderhevig aan de volgende richt lijnen:

* De beschikbaarheids groep-listener wordt ondersteund op Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2.
* Er wordt slechts één interne beschikbaarheids groep-listener ondersteund voor elke Cloud service, omdat de listener is geconfigureerd voor de ILB en er slechts één ILB is voor elke Cloud service. Het is echter mogelijk om meerdere externe listeners te maken. Zie [een externe listener configureren voor AlwaysOn-beschikbaarheids groepen in azure](../classic/ps-sql-ext-listener.md)voor meer informatie.

## <a name="determine-the-accessibility-of-the-listener"></a>De toegankelijkheid van de listener bepalen
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

In dit artikel wordt aandacht besteed aan het maken van een listener die gebruikmaakt van een ILB. Als u een open bare of externe listener nodig hebt, raadpleegt u de versie van dit artikel waarin wordt uitgelegd hoe u een [externe listener](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)kunt instellen.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>VM-eind punten met taak verdeling maken met Direct Server Return
U maakt eerst een ILB door het script verderop in deze sectie uit te voeren.

Maak een eind punt met gelijke taak verdeling voor elke virtuele machine die als host fungeert voor een Azure replica. Als u replica's in meerdere regio's hebt, moet elke replica voor die regio zich in hetzelfde virtuele Azure-netwerk bevinden. Voor het maken van replica's van beschikbaarheids groepen die meerdere Azure-regio's omvatten, moet meerdere virtuele netwerken worden geconfigureerd. Voor meer informatie over het configureren van verbinding tussen virtuele netwerken, Zie [virtuele netwerken configureren voor verbinding met virtueel netwerk](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Ga in het Azure Portal naar elke VM die als host fungeert voor een replica om de details weer te geven.

2. Klik op het tabblad **eind punten** voor elke virtuele machine.

3. Controleer of de **naam** en de **open bare poort** van het listener-eind punt dat u wilt gebruiken niet al in gebruik zijn. In het voor beeld in deze sectie is de naam *MyEndpoint*, en de poort is *1433*.

4. Down load en installeer de meest recente [Power shell-module](https://azure.microsoft.com/downloads/)op de lokale client.

5. Start Azure PowerShell.  
    Er wordt een nieuwe Power shell-sessie geopend, waarbij de Azure-beheer modules zijn geladen.

6. Voer `Get-AzurePublishSettingsFile` uit. Met deze cmdlet wordt u omgeleid naar een browser voor het downloaden van een bestand met publicatie-instellingen naar een lokale map. U wordt mogelijk gevraagd om uw aanmeldings referenties voor uw Azure-abonnement.

7. Voer de volgende `Import-AzurePublishSettingsFile` opdracht uit met het pad van het publicatie-instellingen bestand dat u hebt gedownload:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Nadat u het bestand met de publicatie-instellingen hebt geïmporteerd, kunt u uw Azure-abonnement beheren in de Power shell-sessie.

8. Wijs voor *ILB*een statisch IP-adres toe. Controleer de huidige configuratie van het virtuele netwerk door de volgende opdracht uit te voeren:

        (Get-AzureVNetConfig).XMLConfiguration
9. Noteer de *naam van het subnet voor* het subnet dat de virtuele machines bevat die als host voor de replica's fungeren. Deze naam wordt gebruikt in de para meter $SubnetName in het script.

10. Noteer de naam van de *VirtualNetworkSite* en de eerste *AddressPrefix* voor het subnet met de virtuele machines die de replica's hosten. Zoek naar een beschikbaar IP-adres door beide waarden door te geven aan de `Test-AzureStaticVNetIP` opdracht en door de *AvailableAddresses*te controleren. Als het virtuele netwerk bijvoorbeeld de naam *MyVNet* heeft en een adres bereik voor het subnet heeft dat begint bij *172.16.0.128*, wordt in de volgende opdracht de lijst met beschik bare adressen vermeld:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecteer een van de beschik bare adressen en gebruik deze in de para meter $ILBStaticIP van het script in de volgende stap.

12. Kopieer het volgende Power shell-script naar een tekst editor en stel de waarden van de variabele in op uw omgeving. Voor sommige para meters zijn standaard waarden opgegeven.  

    Bestaande implementaties die gebruikmaken van affiniteits groepen, kunnen geen ILB toevoegen. Zie [overzicht van interne Load Balancer](../../../load-balancer/load-balancer-internal-overview.md)voor meer informatie over ILB-vereisten.

    Als uw beschikbaarheids groep Azure-regio's omvat, moet u ook het script eenmaal uitvoeren in elk Data Center voor de Cloud service en knoop punten die zich in dat Data Center bevinden.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Nadat u de variabelen hebt ingesteld, kopieert u het script van de tekst editor naar uw Power shell-sessie om het uit te voeren. Als de prompt nog steeds wordt weer gegeven **>>** , drukt u nogmaals op ENTER om te controleren of het script wordt uitgevoerd.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Controleer of KB2854082 is geïnstalleerd, indien nodig
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Open de firewall poorten in beschikbaarheids groep knoop punten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>De listener voor beschikbaarheidsgroep maken

Maak de beschikbaarheids groep-listener in twee stappen. Maak eerst de cluster bron voor het client toegangs punt en configureer afhankelijkheden. Ten tweede configureert u de cluster resources in Power shell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Het client toegangs punt maken en de cluster afhankelijkheden configureren
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>De cluster resources configureren in Power shell
1. Voor ILB moet u het IP-adres van de ILB gebruiken die u eerder hebt gemaakt. Gebruik het volgende script om dit IP-adres in Power shell op te halen:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Kopieer op een van de virtuele machines het Power shell-script voor het besturings systeem naar een tekst editor en stel de variabelen in op de waarden die u eerder hebt genoteerd.

    Voor Windows Server 2012 of hoger gebruikt u het volgende script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Voor Windows Server 2008 R2 gebruikt u het volgende script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Nadat u de variabelen hebt ingesteld, opent u een Windows Power shell-venster met verhoogde bevoegdheden en plakt u het script uit de tekst editor in uw Power shell-sessie om het uit te voeren. Als de prompt nog steeds wordt weer gegeven **>>** , drukt u nogmaals op ENTER om te controleren of het script wordt uitgevoerd.

4. Herhaal de voor gaande stappen voor elke virtuele machine.  
    Met dit script wordt de IP-adres bron geconfigureerd met het IP-adres van de Cloud service en worden andere para meters ingesteld, zoals de test poort. Wanneer de IP-adres bron online wordt gebracht, kan deze reageren op de polling op de test poort van het eind punt met gelijke taak verdeling die u eerder hebt gemaakt.

## <a name="bring-the-listener-online"></a>De listener online brengen
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Opvolgings items
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>De listener voor de beschikbaarheids groep testen (binnen hetzelfde virtuele netwerk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
