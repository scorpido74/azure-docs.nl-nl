---
title: Beschikbaarheidsgroeplisteners & load balancer configureren (PowerShell)
description: Gebruikers van beschikbaarheidsgroepen configureren in het Azure Resource Manager-model met behulp van een interne load balancer met een of meer IP-adressen.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f7d14da6c7436120e013c979b108f61b82640d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647880"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Een of meer listeners van de beschikbaarheidsgroep Always On configureren - Resourcebeheer
In dit onderwerp ziet u hoe u:

* Maak een interne load balancer voor SQL Server-beschikbaarheidsgroepen met PowerShell-cmdlets.
* Voeg extra IP-adressen toe aan een load balancer voor meer dan één beschikbaarheidsgroep. 

Een groeplistener voor beschikbaarheid is een virtuele netwerknaam waarmee clients verbinding maken voor databasetoegang. Op virtuele Azure-machines houdt een load balancer het IP-adres voor de listener vast. De load balancer leidt het verkeer naar de instantie van SQL Server die luistert op de sondepoort. Meestal gebruikt een beschikbaarheidsgroep een interne load balancer. Een interne laadbalansr van Azure kan een of meer IP-adressen hosten. Elk IP-adres maakt gebruik van een specifieke sondepoort. In dit document ziet u hoe u PowerShell gebruikt om een load balancer te maken of IP-adressen toe te voegen aan een bestaande load balancer voor SQL Server-beschikbaarheidsgroepen. 

De mogelijkheid om meerdere IP-adressen toe te wijzen aan een interne load balancer is nieuw voor Azure en is alleen beschikbaar in het Resource Manager-model. Als u deze taak wilt voltooien, moet u een SQL Server-beschikbaarheidsgroep hebben geïmplementeerd op virtuele Azure-machines in het Resource Manager-model. Beide virtuele SQL Server-machines moeten tot dezelfde beschikbaarheidsset behoren. U de [Microsoft-sjabloon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) gebruiken om automatisch de beschikbaarheidsgroep te maken in Azure Resource Manager. Met deze sjabloon wordt automatisch de beschikbaarheidsgroep gemaakt, inclusief de interne load balancer voor u. Als u dat liever hebt, u [handmatig een groep Beschikbaarheid always on configureren.](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

Dit onderwerp vereist dat uw beschikbaarheidsgroepen al zijn geconfigureerd.  

Gerelateerde onderwerpen zijn:

* [AlwaysOn-beschikbaarheidsgroepen configureren in Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Een VNet-naar-VNet-verbinding configureren via Azure Resource Manager en PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell-versie verifiëren

De voorbeelden in dit artikel worden getest met Azure PowerShell-module versie 5.4.1.

Controleer of uw PowerShell-module 5.4.1 of hoger is.

Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>De Windows Firewall configureren

Configureer de Windows Firewall om SQL Server-toegang toe te staan. Met de firewallregels kunnen TCP-verbindingen met de poorten worden gebruikt door de SQL Server-instantie en de listenersonde. Zie [Een Windows Firewall voor Database Engine Access configureren voor](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)gedetailleerde instructies. Maak een binnenkomende regel voor de SQL Server-poort en voor de sondepoort.

Als u de toegang beperkt met een Azure Network Security Group, moet u ervoor zorgen dat de regels voor toestaan de IP-adressen van de backend SQL Server VM bevatten en de zwevende IP-adressen van de load balancer voor de AG-listener en het IP-adres van de clusterkern, indien van toepassing.

## <a name="determine-the-load-balancer-sku-required"></a>Bepaal de benodigde load balancer SKU

[Azure load balancer](../../../load-balancer/load-balancer-overview.md) is beschikbaar in 2 SKU's: Basic & Standard. De standaard load balancer wordt aanbevolen. Als de virtuele machines zich in een beschikbaarheidsset bevinden, is basislastbalancer toegestaan. Standaard load balancer vereist dat alle VM IP-adressen standaard IP-adressen gebruiken.

De huidige [Microsoft-sjabloon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) voor een beschikbaarheidsgroep maakt gebruik van een basisloadbalancer met basisIP-adressen.

De voorbeelden in dit artikel geven een standaard load balancer op. In de voorbeelden bevat `-sku Standard`het script .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Als u een basisloadbalancer wilt maken, verwijdert u `-sku Standard` de regel die de load balancer maakt. Bijvoorbeeld:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Voorbeeldscript: een interne load balancer maken met PowerShell

> [!NOTE]
> Als u uw beschikbaarheidsgroep hebt gemaakt met de [Microsoft-sjabloon,](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)is de interne load balancer al gemaakt.

Met het volgende PowerShell-script wordt een interne load balancer gemaakt, worden de regels voor taakverdeling geconfigureerd en wordt een IP-adres ingesteld voor de load balancer. Als u het script wilt uitvoeren, opent u Windows PowerShell ISE en plakt u het script in het Script-deelvenster. Met `Connect-AzAccount` deze gegevens u inloggen op PowerShell. Als u meerdere Azure-abonnementen hebt, gebruikt u `Select-AzSubscription` het abonnement in te stellen. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Voorbeeldscript: Een IP-adres toevoegen aan een bestaande load balancer met PowerShell
Als u meer dan één beschikbaarheidsgroep wilt gebruiken, voegt u een extra IP-adres toe aan de load balancer. Elk IP-adres vereist een eigen regel voor het balanceren van de belasting, de sondepoort en de frontpoort.

De front-endpoort is de poort die toepassingen gebruiken om verbinding te maken met de SQL Server-instantie. IP-adressen voor verschillende beschikbaarheidsgroepen kunnen dezelfde front-endpoort gebruiken.

> [!NOTE]
> Voor SQL Server-beschikbaarheidsgroepen vereist elk IP-adres een specifieke sondepoort. Als bijvoorbeeld één IP-adres op een load balancer sondepoort 59999 gebruikt, kunnen geen andere IP-adressen op die load balancer sondepoort 59999 gebruiken.

* Zie **Private front-end IP per load balancer** onder [Netwerklimieten - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)voor informatie over limieten voor load balancer.
* Zie [Beperkingen (beschikbaarheidsgroepen)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)voor informatie over limieten voor beschikbaarheidsgroepen.

In het volgende script wordt een nieuw IP-adres toegevoegd aan een bestaande load balancer. De ILB gebruikt de listenerpoort voor de load balancing front-end poort. Deze poort kan de poort zijn waarop SQL Server luistert. Voor standaardexemplaren van SQL Server is de poort 1433. De regel voor het balanceren van de belasting voor een beschikbaarheidsgroep vereist een zwevend IP-adres (direct serverretour), zodat de back-endpoort hetzelfde is als de front-endpoort. Werk de variabelen voor uw omgeving bij. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>De listener configureren

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>De listenerpoort instellen in SQL Server Management Studio

1. Start SQL Server Management Studio en maak verbinding met de primaire replica.

1. Navigeer naar**de groepslisteners****beschikbaarheidvan beschikbaarheidsgroepen** | voor **beschikbaarheid alwayson met hoge beschikbaarheid** | . 

1. U moet nu de listenernaam zien die u hebt gemaakt in Failoverclusterbeheer. Klik met de rechtermuisknop op de naam van de listener en klik op **Eigenschappen**.

1. Geef **in** het vak Poort het poortnummer op voor de listener van de beschikbaarheidsgroep met behulp van de $EndpointPort die u eerder hebt gebruikt (1433 was de standaardinstelling) en klik op **OK**.

## <a name="test-the-connection-to-the-listener"></a>De verbinding met de listener testen

Ga als het gaat om het testen van de verbinding:

1. RDP naar een SQL Server die zich in hetzelfde virtuele netwerk bevindt, maar niet de eigenaar is van de replica. Dit kan de andere SQL Server in het cluster zijn.

1. Gebruik **sqlcmd** utility om de verbinding te testen. In het volgende script wordt bijvoorbeeld een **sqlcmd-verbinding** met de primaire replica gemaakt via de listener met Windows-verificatie:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Als de listener een andere poort gebruikt dan de standaardpoort (1433), geeft u de poort in de verbindingstekenreeks op. De volgende sqlcmd-opdracht maakt bijvoorbeeld verbinding met een listener op poort 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

De SQLCMD-verbinding maakt automatisch verbinding met de instantie van SQL Server die de primaire replica host. 

> [!NOTE]
> Zorg ervoor dat de poort die u opgeeft, is geopend op de firewall van beide SQL-servers. Beide servers vereisen een inkomende regel voor de TCP-poort die u gebruikt. Zie [Firewallregel toevoegen of bewerken](https://technet.microsoft.com/library/cc753558.aspx) voor meer informatie. 
> 
> 

## <a name="guidelines-and-limitations"></a>Richtlijnen en beperkingen
Let op de volgende richtlijnen voor de listener van de beschikbaarheidsgroep in Azure met behulp van interne load balancer:

* Met een interne load balancer hebt u alleen toegang tot de listener vanuit hetzelfde virtuele netwerk.

* Als u de toegang beperkt met een Azure Network Security Group, moet u ervoor zorgen dat de regels voor toestaan de IP-adressen van de backend SQL Server VM bevatten en de zwevende IP-adressen van de load balancer voor de AG-listener en het IP-adres van de clusterkern, indien van toepassing.

## <a name="for-more-information"></a>Voor meer informatie
Zie [Beschikbaarheidsgroep Altijd op configureren in Azure VM handmatig](virtual-machines-windows-portal-sql-availability-group-tutorial.md)voor meer informatie.

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets
Gebruik de volgende PowerShell-cmdlets om een interne load balancer voor virtuele Azure-machines te maken.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) maakt een load balancer. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) creëert een front-end IP-configuratie voor een load balancer. 
* [Nieuw-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) maakt een regelconfiguratie voor een load balancer. 
* [Nieuw-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) maakt een backend-adresgroepconfiguratie voor een load balancer. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) maakt een sondeconfiguratie voor een load balancer.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) verwijdert een load balancer uit een Azure-brongroep.
