---
title: Een ILB-listener configureren voor beschikbaarheidsgroepen (Klassiek)
description: In deze zelfstudie worden resources gebruikt die zijn gemaakt met het klassieke implementatiemodel en wordt een always on-groeplistener voor beschikbaarheid gemaakt voor een SQL Server VM in Azure die een interne load balancer gebruikt.
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
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978171"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Een ILB-listener configureren voor beschikbaarheidsgroepen op Azure SQL Server VM's
> [!div class="op_single_selector"]
> * [Interne listener](../classic/ps-sql-int-listener.md)
> * [Externe listener](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Azure Resource Manager en classic](../../../azure-resource-manager/management/deployment-models.md). Dit artikel behandelt het gebruik van het klassieke implementatiemodel. We raden u aan dat de meeste nieuwe implementaties het Resource Manager-model gebruiken.

Zie [Een load balancer configureren voor een groep beschikbaarheid always on in Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)als u een listener wilt configureren voor een groep beschikbaarheid always on in het resourcebeheer.

Uw beschikbaarheidsgroep kan replica's bevatten die alleen on-premises of Azure zijn, of die zowel on-premises als Azure voor hybride configuraties omvatten. Azure-replica's kunnen zich binnen dezelfde regio of in meerdere regio's bevinden die meerdere virtuele netwerken gebruiken. De procedures in dit artikel gaan ervan uit dat u [al een beschikbaarheidsgroep](../classic/portal-sql-alwayson-availability-groups.md) hebt geconfigureerd, maar nog geen listener hebt geconfigureerd.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Richtlijnen en beperkingen voor interne luisteraars
Het gebruik van een iLB (internal load balancer) met een authenticiteitsgroeplistener in Azure is onderworpen aan de volgende richtlijnen:

* De listener van de beschikbaarheidsgroep wordt ondersteund op Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2.
* Voor elke cloudservice wordt slechts één interne beschikbaarheidsgroeplistener ondersteund, omdat de listener is geconfigureerd voor de ILB en er slechts één ILB voor elke cloudservice is. Het is echter mogelijk om meerdere externe luisteraars te maken. Zie [Een externe listener configureren voor beschikbaarheidsgroepen altijd in Azure](../classic/ps-sql-ext-listener.md)voor meer informatie.

## <a name="determine-the-accessibility-of-the-listener"></a>De toegankelijkheid van de luisteraar bepalen
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Dit artikel richt zich op het maken van een luisteraar die een ILB gebruikt. Als u een openbare of externe listener nodig hebt, raadpleegt u de versie van dit artikel waarin wordt gesproken over het instellen van een [externe listener.](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Load-balanced VM-eindpunten maken met direct serverrendement
U maakt eerst een ILB door het script later in deze sectie uit te voeren.

Maak een eindpunt voor elke vm die een Azure-replica host. Als u replica's in meerdere regio's hebt, moet elke replica voor die regio zich in dezelfde cloudservice in hetzelfde virtuele Azure-netwerk bevinden. Voor het maken van replica's van beschikbaarheidsgroepen die meerdere Azure-regio's omvatten, moeten meerdere virtuele netwerken worden geconfigureerd. Zie Virtueel netwerk configureren voor virtuele netwerkconnectiviteit voor meer informatie over het configureren van virtuele [netwerkconnectiviteit.](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

1. Ga in de Azure-portal naar elke vm met een replica om de details weer te geven.

2. Klik op het tabblad **Eindpunten** voor elke virtuele machine.

3. Controleer of de **naam** en **de openbare poort** van het listenereindpunt dat u wilt gebruiken, nog niet in gebruik zijn. In het voorbeeld in deze sectie is de naam *MyEndpoint*en de poort *1433*.

4. Download en installeer op uw lokale client de nieuwste [PowerShell-module.](https://azure.microsoft.com/downloads/)

5. Start Azure PowerShell.  
    Er wordt een nieuwe PowerShell-sessie geopend, waarbij de Azure-beheermodules zijn geladen.

6. Voer `Get-AzurePublishSettingsFile` uit. Deze cmdlet leidt u naar een browser om een bestand met publicatie-instellingen te downloaden naar een lokale map. Mogelijk wordt u gevraagd om uw aanmeldingsreferenties voor uw Azure-abonnement.

7. Voer de `Import-AzurePublishSettingsFile` volgende opdracht uit met het pad van het bestand met publicatie-instellingen dat u hebt gedownload:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Nadat het publicatie-instellingenbestand is geïmporteerd, u uw Azure-abonnement beheren in de PowerShell-sessie.

8. Wijs voor *ILB*een statisch IP-adres toe. Onderzoek de huidige virtuele netwerkconfiguratie door de volgende opdracht uit te voeren:

        (Get-AzureVNetConfig).XMLConfiguration
9. Let op de *subnetnaam* voor het subnet dat de VM's bevat die de replica's hosten. Deze naam wordt gebruikt in de parameter $SubnetName in het script.

10. Let op de naam *VirtualNetworkSite* en het *beginadresvoorvoegsel* voor het subnet dat de VM's bevat die de replica's hosten. Zoek naar een beschikbaar IP-adres `Test-AzureStaticVNetIP` door beide waarden door te geven aan de opdracht en door de *beschikbare adressen te*onderzoeken. Als het virtuele netwerk bijvoorbeeld *MyVNet* heet en een subnetadresbereik heeft dat begint bij *172.16.0.128,* wordt in de volgende opdracht beschikbare adressen weergegeven:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecteer een van de beschikbare adressen en gebruik deze in de parameter $ILBStaticIP van het script in de volgende stap.

12. Kopieer het volgende PowerShell-script naar een teksteditor en stel de variabele waarden in op uw omgeving. Standaardinstellingen zijn voorzien voor sommige parameters.  

    Bestaande implementaties die affiniteitsgroepen gebruiken, kunnen geen ILB toevoegen. Zie [Overzicht interne load balancer](../../../load-balancer/load-balancer-internal-overview.md)voor meer informatie over iLB-vereisten.

    Als uw beschikbaarheidsgroep Azure-regio's omvat, moet u het script ook eenmaal in elk datacenter uitvoeren voor de cloudservice en knooppunten die zich in dat datacenter bevinden.

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

13. Nadat u de variabelen hebt ingesteld, kopieert u het script van de teksteditor naar uw PowerShell-sessie om het uit te voeren. Als de prompt **>>** nog steeds wordt weergegeven, drukt u nogmaals op Enter om te controleren of het script wordt uitgevoerd.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Controleer indien nodig of KB2854082 is geïnstalleerd
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>De firewallpoorten openen in knooppunten van beschikbaarheidsgroepen
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>De authenticiteit van de beschikbaarheidsgroep maken

Maak de listener van de beschikbaarheidsgroep in twee stappen. Maak eerst de clientaccess point clusterbron en configureer afhankelijkheden. Configureer ten tweede de clusterbronnen in PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Het toegangspunt van de client maken en de clusterafhankelijkheden configureren
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>De clusterbronnen in PowerShell configureren
1. Voor ILB moet u het IP-adres van de ILB gebruiken dat eerder is gemaakt. Gebruik het volgende script om dit IP-adres in PowerShell te verkrijgen:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Kopieer op een van de VM's het PowerShell-script voor uw besturingssysteem naar een teksteditor en stel de variabelen in op de waarden die u eerder hebt opgemerkt.

    Gebruik voor Windows Server 2012 of hoger het volgende script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Gebruik voor Windows Server 2008 R2 het volgende script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Nadat u de variabelen hebt ingesteld, opent u een verhoogd Windows PowerShell-venster en plakt u het script van de teksteditor in uw PowerShell-sessie om het uit te voeren. Als de prompt **>>** nog steeds wordt weergegeven, drukt u nogmaals op Enter om ervoor te zorgen dat het script wordt uitgevoerd.

4. Herhaal de voorgaande stappen voor elke virtuele machine.  
    Dit script configureert de IP-adresbron met het IP-adres van de cloudservice en stelt andere parameters in, zoals de sondepoort. Wanneer de IP-adresbron online wordt gebracht, kan deze reageren op de polling op de sondepoort vanaf het eerder gemaakte eindpunt voor de belasting.

## <a name="bring-the-listener-online"></a>Breng de luisteraar online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Vervolgitems
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>De listener voor beschikbaarheidsgroepen testen (binnen hetzelfde virtuele netwerk)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
