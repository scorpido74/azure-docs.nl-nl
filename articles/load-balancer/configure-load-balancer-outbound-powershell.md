---
title: Load balancing en uitgaande regels configureren met Azure PowerShell
titleSuffix: Azure Load Balancer
description: In dit artikel ziet u hoe u load balancing en outbound rules configureert in Standard Load Balancer met Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225444"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Load balancing en outbound rules configureren in Standard Load Balancer met Azure PowerShell

In dit artikel ziet u hoe u uitgaande regels configureert in Standard Load Balancer met Azure PowerShell.  

Wanneer u het scenario van dit artikel voltooit, bevat de resource load balancer twee front-ends en de bijbehorende regels. U hebt één front-end voor binnenkomend verkeer en een andere front-end voor uitgaand verkeer.  

Elke front-end verwijst naar een openbaar IP-adres. In dit scenario verschilt het openbare IP-adres voor binnenkomend verkeer van het adres voor uitgaand verkeer.   De regel voor het balanceren van de regel biedt alleen inkomende taakverdeling. De regel voor uitgaande geldt voor de uitgaande netwerkadresvertaling (NAT) voor de VM.  

Het scenario maakt gebruik van twee back-endpools: een voor binnenkomend verkeer en een voor uitgaand verkeer. Deze pools illustreren de mogelijkheden en bieden flexibiliteit voor het scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account
Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Volg vervolgens de aanwijzingen op het scherm.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met [Nieuw-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Een Azure-brongroep is een logische container waarin Azure-resources zijn geïmplementeerd. De resources worden vervolgens beheerd vanuit de groep.

In het volgende voorbeeld wordt een resourcegroep met de naam *myresourcegroupoutbound* op de locatie *eastus2* gea...

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk met de naam *myvnetoutbound*. Naam van het subnet *mysubnetoutbound*. Plaats het in *myresourcegroupoutbound* met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) en [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Een binnenkomend openbaar IP-adres maken 

Om toegang te krijgen tot uw web-app op het internet, hebt u een openbaar IP-adres nodig voor de load balancer. Standard Load Balancer ondersteunt alleen standaard openbare IP-adressen. 

Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) om een standaard openbaar IP-adres met de naam *mypublicipinbound* te maken in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Een uitgaand openbaar IP-adres maken 

Maak een standaard IP-adres voor de front-end uitgaande configuratie van de load balancer met behulp van [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Een Azure-load balancer maken

In deze sectie wordt uitgelegd hoe u de volgende onderdelen van de load balancer maakt en configureert:
  - Een front-end IP dat het binnenkomende netwerkverkeer op de load balancer ontvangt
  - Een back-end pool waar het front-end IP het load-balanced netwerkverkeer verzendt
  - Een back-end pool voor uitgaande connectiviteit
  - Een statussonde die de status van de back-end VM-exemplaren bepaalt
  - Een inkomende regel voor load-balancer die bepaalt hoe verkeer wordt gedistribueerd naar de VM's
  - Een outbound-regel voor load-balancer die bepaalt hoe verkeer wordt gedistribueerd vanuit de VM's

### <a name="create-an-inbound-front-end-ip"></a>Een binnenkomend front-end IP maken
Maak de inkomende front-end IP-configuratie voor de load balancer met [New-AzLoadBalancerFrontendIpConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) De load balancer moet een inkomende front-end IP-configuratie met de naam *myfrontendinbound*bevatten. Deze configuratie koppelen aan het openbare IP-adres *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Een uitgaand front-end IP maken
Maak de uitgaande front-end IP-configuratie voor de load balancer met behulp van [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Deze load balancer moet een uitgaande front-end IP-configuratie met de naam *myfrontendoutbound*bevatten. Deze configuratie koppelen aan het openbare IP-adres *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Een binnenkomende back-endpool maken
Maak de back-end inkomende pool voor de load balancer met [New-AzLoadBalancerBackendAddressConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Geef de naam van het zwembad *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Een uitgaande back-endpool maken
Gebruik de volgende opdracht om een andere back-end-adresgroep te maken om uitgaande connectiviteit voor een groep VM's te definiëren met behulp van [Nieuw-AzLoadBalancerBackendAddressConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Geef deze groep *bepooloutbound*een naam. 

Door een aparte uitgaande pool te maken, biedt u maximale flexibiliteit. Maar u deze stap weglaten en alleen de binnenkomende *bepoolinbound* gebruiken als u dat liever hebt.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Een statustest maken

Een statussonde controleert alle VM-exemplaren om te controleren of ze netwerkverkeer kunnen verzenden. De VM-instantie die niet slaagt voor de sondecontroles wordt verwijderd uit de load balancer totdat deze weer online gaat en een sondecontrole bepaalt of deze in orde is. 

Als u de status van de VM's wilt controleren, maakt u een statussonde met [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Een regel load-balancer maken

Een load-balancer-regel definieert de front-end IP-configuratie voor het binnenkomende verkeer en de back-endpool om het verkeer te ontvangen. Het definieert ook de vereiste bron- en bestemmingspoort. 

Maak een regel voor load-balancer met de naam *myinboundlbrule* met behulp van [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Deze regel zal luisteren naar poort 80 in de front-end zwembad *myfrontendinbound*. Het zal ook gebruik maken van poort 80 om load-balanced netwerkverkeer te sturen naar de back-end adres pool *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Met deze regel voor het balanceren van de lastenschakelt schakelt automatische uitgaande beveiligde NAT (SNAT) uit vanwege de parameter **-DisableOutboundSNAT.** Uitgaande NAT wordt alleen geleverd door de uitgaande regel.

### <a name="create-an-outbound-rule"></a>Een uitgaande regel maken

Een uitgaande regel definieert het front-end openbare IP, dat wordt vertegenwoordigd door de front-end *myfrontendoutbound*. Deze front-end zal worden gebruikt voor alle uitgaande NAT verkeer, evenals de back-end pool waarop de regel van toepassing is.  

Gebruik de volgende opdracht om een uitgaande regel *myoutboundrule* te maken voor uitgaande netwerkvertaling van alle VM's (in NIC IP-configuraties) in de back-endpool van *Bepool.*  De opdracht wijzigt de uitgaande idle time-out van 4 naar 15 minuten. Het wijst 10.000 SNAT-poorten toe in plaats van 1.024. Zie [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)voor meer informatie.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Als u geen aparte uitgaande groep wilt gebruiken, u het argument adresgroep in de vorige opdracht wijzigen om *in plaats daarvan $bepoolin* op te geven.  We raden u aan afzonderlijke pools te gebruiken om de resulterende configuratie flexibel en leesbaar te maken.

### <a name="create-a-load-balancer"></a>Een load balancer maken

Gebruik de volgende opdracht om een load balancer voor het binnenkomende IP-adres te maken met [Nieuw-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Geef de load balancer *lb een naam.* Het moet een inkomende front-end IP-configuratie bevatten. De back-end pool *bepoolinbound* moet worden gekoppeld aan het openbare IP-adres *mypublicipinbound* dat u in de vorige stap hebt gemaakt.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Op dit punt u doorgaan met het toevoegen van uw VM's aan zowel *bepoolinbound* als *bepooloutbound* back-end pools door de IP-configuratie van de respectievelijke NIC-bronnen bij te werken. Werk de resourceconfiguratie bij met [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep, de load balancer en de bijbehorende resources niet meer nodig hebt, u deze verwijderen met [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een standaardloadbalancer gemaakt, zowel inkomende als uitgaande belast-balancerverkeersregels geconfigureerd en een statussonde geconfigureerd voor de VM's in de back-endpool. 

Ga voor meer informatie verder naar de [zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
